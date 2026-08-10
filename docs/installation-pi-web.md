# Install pi-web for pi

[pi-web](https://github.com/agegr/pi-web) is a **local web UI** for pi. It runs as a separate process that reads your on-disk pi sessions (`~/.pi/agent/sessions/<cwd>/<id>.jsonl`) and serves a browser workspace at <http://127.0.0.1:30141>: session browser, real-time chat, model / provider configuration, skill toggle, file tree, and source / document / image / PDF preview — side by side with the live conversation.

> pi-web is **not** a pi extension. pi itself never sees it. pi-web only reads the same `.jsonl` files that pi writes, and it exposes a small HTTP API for the browser. Stop it any time and pi keeps running exactly as before.

## Prerequisites

- Node.js **22.19.0** or newer (`node --version`)
- pi installed and used at least once (so `~/.pi/agent/sessions/` has data to read)

## Install

You don't need any pi-side installation — pi-web is a standalone CLI. Install the published package globally so the `pi-web` command lands on your `PATH`, then launch it:

```bash
npm install -g @agegr/pi-web@latest
pi-web
```

The launcher binds `127.0.0.1:30141` by default (only reachable from the same machine) and tries to open the browser automatically when the server is ready. Disable auto-open with `--no-open` or `PI_WEB_NO_OPEN=1`.

## Verify

Open <http://127.0.0.1:30141> in any browser on the same machine. You should see the Pi Web workspace — a left sidebar with the project tree, a session list, and an empty conversation pane (or your most recent pi conversation if you've used pi in the same directory before).

A few quick sanity checks from the same shell:

```bash
# Is it still running?
curl -sI http://127.0.0.1:30141 | head -n 1
# Should print: HTTP/1.1 200 OK
```

## Usage

| UI area | What it gives you |
| --- | --- |
| **Project / session browser** (left sidebar) | Every past pi conversation for the directory, grouped by project; click to reload |
| **Worktree switcher** | Switch the active Git worktree — new sessions and the file explorer follow the checkout you pick |
| **File tree + preview** (right pane) | Browse project files; preview source, Markdown, images, audio, and PDFs inline |
| **Live conversation** (centre pane) | The same chat stream you'd see in pi, but rendered with structured Markdown and tool-call blocks |
| **Status bar** (top) | Context usage, cost, compaction state, and the active system prompt — visible without scrolling |
| **Models panel** | Read / write `models.json` in your pi agent dir; switch providers, test keys, toggle skills |
| **Language switcher** | Switch the UI between the supported languages from the top bar |

The **fork / resume** affordances let you keep multiple parallel directions from an earlier turn — handy when you want to try a refactor without abandoning the working branch.

## Configure

pi-web reads its configuration from CLI flags and a small set of environment variables. None are required for a local-only install.

| Variable / flag | Effect |
| --- | --- |
| `PORT`, `--port` | Override the listen port (default `30141`) |
| `PI_WEB_HOSTNAME` / `--hostname` | Bind interface — `0.0.0.0` exposes on a trusted network |
| `PI_WEB_ALLOWED_HOSTS` | Comma-separated hostnames the API will accept (use this when a reverse proxy fronts the server on a different hostname) |
| `PI_WEB_PASSWORD` | Require HTTP Basic Auth on every endpoint. Username is always `pi`; pick a long random value. **Disables auth if unset or empty.** |
| `PI_WEB_NO_OPEN=1` / `--no-open` | Skip the auto-open-browser step (use when running headless) |
| `HTTP_PROXY` / `HTTPS_PROXY` / `NO_PROXY` | Standard proxy variables for the server-side model / API requests |

Examples:

```bash
# Custom port
pi-web --port 8080

# Expose on a trusted LAN (don't forget auth + HTTPS in front)
pi-web --hostname 0.0.0.0

# Protect with Basic Auth
PI_WEB_PASSWORD='a-long-random-password' pi-web

# Behind a proxy in CI / Docker
HTTP_PROXY=http://127.0.0.1:7890 \
HTTPS_PROXY=http://127.0.0.1:7890 \
NO_PROXY=localhost,127.0.0.1 \
pi-web
```

### Reach pi-web from another machine

Out of the box, pi-web binds only to loopback. Three ways to widen that safely:

1. **VPN / WireGuard / Tailscale** — bind on `0.0.0.0`, set `PI_WEB_PASSWORD`, and reach it via the VPN. The connection is private by construction.
2. **Trusted reverse proxy + HTTPS** — terminate TLS in nginx / Caddy / Traefik, set `PI_WEB_ALLOWED_HOSTS` to the external hostname you proxy with, and set `PI_WEB_PASSWORD`. Avoid plain HTTP over the public internet.
3. **SSH port-forward** — `ssh -L 30141:127.0.0.1:30141 your-host` and visit <http://127.0.0.1:30141> from your laptop. Auth not needed (the channel is already authenticated).

> pi-web can drive a fully-privileged pi agent (it serves session messages to and from a local pi process). Don't expose the unauthenticated HTTP port to the open internet, even with `--hostname 0.0.0.0`.

### Data directory

By default pi-web reads sessions from **`~/.pi/agent/sessions`** and writes `models.json` into the same dir. Point it at another pi agent directory with `PI_CODING_AGENT_DIR`:

```bash
PI_CODING_AGENT_DIR=/path/to/another/pi-agent pi-web
```

Run pi-web in the same OS / container as pi — session working directories need to be reachable by both.

## Uninstall

```bash
npm uninstall -g @agegr/pi-web      # if you installed globally
```

That's it. **pi-web holds no state of its own** — when it's not running, the browser-tab integration vanishes and only the on-disk pi sessions remain. There is nothing else to remove; your pi install is untouched.

## Scope

pi-web only adds a browser-side viewer and a small HTTP API that reads / writes the same files pi manages (`~/.pi/agent/sessions/*.jsonl`, `models.json`). It does **not** modify pi, install any extension, change pi's system prompt, default tool list, model selection, or session lifecycle. Stop the `pi-web` process and pi keeps running as if nothing happened.
