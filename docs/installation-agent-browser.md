# Install agent-browser

> [agent-browser](https://github.com/vercel-labs/agent-browser) lets the pi agent control a browser via CDP (Chrome DevTools Protocol) — more token-efficient than traditional headless browser approaches.

## Prerequisites

```bash
which agent-browser || npm i -g agent-browser && agent-browser install
```

`agent-browser install` downloads the required Chromium binary.

## One-line install

`cd` into the project directory, then run:

```
npx skills add https://github.com/vercel-labs/agent-browser --skill agent-browser --agent pi -y
```

This installs the skill into the project's `.pi/skills/` directory.

## Usage

In a pi session, the agent can now drive the browser via the `agent-browser` skill — open pages, click elements, fill forms, take screenshots, and read console / network output. Ask the agent to do something on a real browser and it will invoke the skill.

## Recommendation: install at the project level

- Scopes the skill to projects that actually need browser automation
- Keeps the agent's tool list clean — only projects with web testing get the browser tools
- Matches the intended usage pattern (per-project, not per-machine)

## Avoid: global install

Global install adds browser tools to every project session, wasting tokens and context on projects that don't need it. Not recommended.