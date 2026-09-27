# Port_ skills

Agent skills and a plugin for reading your own [Port_](https://getport.app) port from Claude
Code, Codex and anything else that speaks MCP or reads an Agent Skills folder: net worth,
holdings, DeFi, perps, prediction markets, NFTs, PnL, activity, the daily briefing and alerts,
with what is not counted and how old it is.

Read only. Nothing here can move funds, sign a transaction or change a setting. Everything goes
over `https://getport.app` with your own key, so this repository is a pointer, not a client.

## What you need

A Port_ Pro account and an API key, made under Settings, API access, at
[getport.app/settings/api](https://getport.app/settings/api). The key is shown once. Keep it in
the `PORT_API_KEY` environment variable and never paste it into a prompt.

claude.ai, Claude Desktop and ChatGPT need no key and no install: add a custom connector with
the address `https://getport.app/mcp` and sign in at getport.app when it asks.

## Install

Claude Code:

```text
/plugin marketplace add FitzDegenhub/port-skills
/plugin install port@port
```

It asks for the key when the plugin is enabled and keeps it in the system's credential store.

Codex:

```bash
codex plugin marketplace add FitzDegenhub/port-skills
codex plugin add port@port
```

Codex reads the key from `PORT_API_KEY`.

Any tool that installs skills from a site:

```bash
npx skills add https://getport.app
```

Or connect the MCP server by hand: `port-mcp/SKILL.md` has the block for Claude Code, Codex,
Cursor and VS Code.

## What is in here

| Path | What it is |
| ---- | ---------- |
| `port-api/` | The skill for the REST API, with every endpoint in `references/endpoints.md` |
| `port-mcp/` | The skill for the MCP server at `https://getport.app/mcp` and how to connect it |
| `.claude-plugin/` | The Claude Code marketplace and plugin manifest |
| `.mcp.json` | The MCP server the Claude Code plugin registers, with your key as the header |
| `.agents/plugins/`, `.codex-plugin/` | The Codex marketplace and plugin manifest |

## How it is made

This repository is published from the Port_ codebase and is overwritten on every change, so a
pull request here would be lost. The endpoint reference is generated from the same table the API
itself is built from. Docs: [docs.getport.app](https://docs.getport.app/api/).
