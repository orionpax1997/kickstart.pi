# Install remote-pi for pi

[remote-pi](https://pi.dev/packages/remote-pi) adds two independent superpowers on top of pi, wired up by a single `/remote-pi` slash command:

1. **Agent network** — open several pi terminals side-by-side in the same directory and they discover each other, exchanging messages via a local Unix-domain-socket broker. Each session gets two tools the LLM can call directly: `agent_send` (fire-and-forget) and `agent_request` (send and await a reply).
2. **Mobile app** — a companion phone app ([Remote Pi](https://remote-pi.jacobmoura.work/)) drives pi from your pocket. Pair with a QR code, send prompts / voice / images, switch models and thinking levels. The phone and pi find each other through a small WebSocket relay.

The agent network is purely local (no network involved); only the mobile app touches the network, and only through a TLS-encrypted WebSocket whose payload is end-to-end encrypted between pi and the paired device. See [Trust model](#trust-model) for what the relay actually sees.

> remote-pi is unrelated to [pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents): sub-agents live inside one process and the main agent spawns them; remote-pi peers are **separate pi processes** that opt in to a shared mesh and talk to each other directly.

## Prerequisites

- Node 20+
- pi installed (`pi --version` to confirm)

## Install

```bash
pi install npm:remote-pi
```

This adds the package to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup; the extension also auto-deploys the `agent-network` skill into `~/.pi/remote/skills/` so the LLM knows how to use `agent_send` / `agent_request` correctly.

> Install at the **global** level: the `/remote-pi` slash command, the `agent_send` / `agent_request` tools, and the pairing state at `~/.pi/remote/peers.json` are all session- / machine-level. One install covers every project.

## Verify

In a fresh `pi` session:

```bash
/remote-pi config
```

It should print the effective relay URL and where it came from (`env` / `config` / `default`). On the very first run it'll show the setup wizard instead — see next section.

You can also confirm the LLM-side wiring by asking pi:

```
What remote-pi tools do you have available?
```

It should list `agent_send` (and `agent_request`).

## First-run setup

`/remote-pi` with no local config (`./.pi/remote-pi/config.json`) launches a short wizard that asks three things:

1. **Agent name** — how other peers will address you in the mesh. Defaults to the directory name.
2. **Default session** — the room name for this directory. Multiple terminals `cd`'d into the same directory join the same session automatically.
3. **Auto-start the relay** — say *Yes* if you want the mobile app to reach this pi; *No* if you only want the local agent network.

After the wizard saves config, pi joins the local session and (if you opted in) starts the relay. Subsequent `/remote-pi` runs join + start in one shot.

Re-run the wizard later with `/remote-pi setup`.

## What it adds

### 1) Agent network (local)

Two tools become available to the LLM, backed by a Unix-domain-socket broker at `~/.pi/remote/sessions/<session>/broker.sock`. The first peer to enter a session hosts the broker; if it exits, a follower takes over — failover is invisible to the LLMs.

```ts
// Fire-and-forget. Delivery is reliable; status returned to the caller.
agent_send({
  to: "backend",            // peer name, or array for multicast, or "broadcast"
  body: { task: "add /healthz endpoint" },
  re: "<incoming-id>",      // optional — set when replying to a request
})

// Send + await reply (default 30s timeout). Blocks the calling turn.
agent_request({
  to: "backend",
  body: { question: "is the migration applied?" },
})
```

Names that collide inside a session get a `#N` suffix automatically (`backend`, `backend#2`, …). The broker writes an audit log to `~/.pi/remote/sessions/<session>/audit.jsonl` for postmortem inspection.

> **Prefer `agent_send` over `agent_request`.** `agent_request` blocks the calling turn waiting for a reply (costs tokens + wall time). `agent_send` returns a delivery ACK immediately and any reply arrives on a later turn — the model the `agent-network` skill is designed around.

**Try it in 30 seconds**: open two `pi` terminals in the same directory, run `/remote-pi` in each. In terminal A:

```
Who else is connected in our agent session? List them.
Send a ping to <the-other-name> and wait for a reply.
```

Terminal B's LLM gets the ping as a user-facing turn, answers, and the reply lands back in A.

### 2) Mobile app (via the relay)

The relay is the only network-touching piece. It ferries opaque `ct` blobs between paired devices and the pi process — payloads are end-to-end encrypted (the relay cannot read them), but it does see connection metadata (which keypair is online, which room/cwd identifiers exist, message timing and sizes).

Pairing is per machine via a one-time QR code:

```bash
/remote-pi pair      # prints QR + copy-paste URI in the terminal
/remote-pi devices   # list paired devices (online / offline)
/remote-pi revoke <shortid>   # remove a device (shortid = first 8 chars of `devices`)
```

Scan the QR with the [Remote Pi mobile app](https://remote-pi.jacobmoura.work/). Once paired, every pi process on this machine accepts the device (state lives in `~/.pi/remote/peers.json`).

The app exposes a small set of typed actions plus free-form chat with voice and one attached image (camera or gallery). Each action is wired to a clean SDK call:

| App action | Effect on pi |
| --- | --- |
| Compact context | `ctx.compact()` — same as `/compact` in the TUI |
| New session | `ctx.newSession()` — same as `/new`, asks for confirmation first |
| Model | Opens a picker fed by your authenticated providers, then `pi.setModel(model)` |
| Thinking | 6-level segmented control (`off` · `minimal` · `low` · `medium` · `high` · `xhigh`) → `pi.setThinkingLevel(level)` |

Each action returns a structured `action_ok` / `action_error` so the app can render a SnackBar on failure. Visible side-effects (chat output, model-change broadcasts, compaction notice) flow through the normal chat channels.

Images ride inline inside the `user_message` envelope as `{ data, mime }` and the extension turns them into the SDK's multimodal content. The image-attachment button is greyed out automatically when the active model doesn't accept images.

## Slash command reference

### Local session

| Command | Description |
| --- | --- |
| `/remote-pi` | Connect (join local mesh + start relay), or run setup on first use |
| `/remote-pi setup` | Re-run the setup wizard and update local config |
| `/remote-pi status` | Show local mesh + relay status |
| `/remote-pi stop` | Stop everything for this terminal (mesh + relay) |
| `/remote-pi join [name]` | Join (or create) a session — only needed manually if auto-start is off |
| `/remote-pi leave` | Leave the current session |
| `/remote-pi sessions` | List local sessions and which are live |
| `/remote-pi rename <name>` | Rename this agent in the current session |
| `/remote-pi pair` | Show QR + copy-paste URI for a new mobile device |
| `/remote-pi devices` | List paired mobile devices (online / offline per device) |
| `/remote-pi revoke <shortid>` | Revoke a paired device by its shortid |
| `/remote-pi relay url <url>` | Persist a new relay URL (`http://` or `https://`) |
| `/remote-pi relay status` | Show whether the relay is `stopped` / `started` / `paired` |
| `/remote-pi relay start` / `stop` | Toggle the relay |
| `/remote-pi config` | Print effective config (relay URL + source) |

### Daemon fleet (optional, advanced)

The supervisor mode runs registered folders as 24/7 background daemons — useful if you want pi to keep working after you close the terminal, or to schedule recurring prompts.

| Command | Description |
| --- | --- |
| `/remote-pi create [--name X]` | Register a folder as a daemon |
| `/remote-pi remove <name>` | Unregister a daemon (local config preserved) |
| `/remote-pi daemons` | List registered daemons + state |
| `/remote-pi daemon start` / `stop` / `restart` | Lifecycle on the full fleet |
| `/remote-pi daemon status` | Detailed runtime status (pid, uptime, restart count) |
| `/remote-pi daemon send "<name>" "<prompt>"` | Send a prompt to a specific daemon |
| `/remote-pi cron add "<id>" "<expr>" "<prompt>"` | Schedule a recurring prompt (optional `--tz`, `--wake`, `--no-skip-busy`, `--catchup`) |
| `/remote-pi cron list` / `run` / `enable` / `disable` / `remove` / `log` | Manage scheduled jobs |
| `/remote-pi install` / `uninstall` | Install `pi-supervisord` as a system service |

Minimum cron interval is 60s. Cron only runs while the supervisor is up — without `remote-pi install`, the `cron` commands refuse rather than silently doing nothing.

All of the above also work as shell-level `remote-pi <subcommand>` when the package is installed globally (`npm install -g remote-pi`).

## Trust model

You have two relay options. Either way, the **payloads** between pi and the paired phone are end-to-end encrypted; the relay only sees opaque `ct` blobs. What the relay does see is connection metadata.

### Option A — Community relay (default)

`https://relay-rp1.jacobmoura.work`. Zero setup. Good for trying things out or casual use. Caveats: shared infrastructure, best-effort availability, the operator can observe the metadata described above.

### Option B — Self-host (recommended for privacy)

Run the relay in Docker and put it behind a VPN (Tailscale / WireGuard / your own VPC). Because the relay's network-level protection is just TLS + keypair auth, layering a VPN means only your devices can reach the WebSocket port.

```bash
docker run -d \
  --name remote-pi-relay \
  -p 3000:3000 \
  --restart unless-stopped \
  ghcr.io/jacobaraujo7/remote-pi-relay:latest
```

Bind the container to your VPN interface, terminate TLS in a reverse proxy, then point both pi and the phone at the resulting `https://…` URL.

```bash
/remote-pi relay url https://relay.yourdomain.tld
```

URL resolution order (highest precedence first):

1. `REMOTE_PI_RELAY` environment variable (CI / one-off overrides)
2. `~/.pi/remote/config.json`
3. Built-in default

Re-verify with `/remote-pi config`. The mobile app has its own relay-URL setting in preferences — keep both pointing at the same relay.

## Uninstall

```bash
pi remove npm:remote-pi
```

Removes the entry from `~/.pi/agent/settings.json`. **Note**: local state in `~/.pi/remote/` (sessions, audit logs, paired device list in `peers.json`, supervisor registry, scheduled cron jobs) is **not** deleted. To clean up everything:

```bash
rm -rf ~/.pi/remote
```

Do this only if you want a fully fresh start; otherwise just `pi remove` and your local config will be picked up again if you reinstall later.

## Scope

remote-pi adds two LLM-callable tools (`agent_send`, `agent_request`), one slash command (`/remote-pi`), the auto-deployed `agent-network` skill, and the pairing / relay plumbing for the mobile app. It does **not** change pi's main agent behavior, system prompt, default tool list, model selection, or session lifecycle.