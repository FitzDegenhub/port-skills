# Port_ skills

Agent skills and a plugin for reading your own [Port_](https://getport.app) port from Claude
Code, Codex and anything else that speaks MCP or reads an Agent Skills folder: net worth,
holdings, DeFi, perps, prediction markets, NFTs, PnL, activity, the daily briefing and alerts,
with what is not counted and how old it is.

Read only. Nothing here can move funds, sign a transaction or change a setting. Everything goes
over `https://getport.app`, so this repository is a pointer, not a client.

## Install

One command connects every coding agent on your machine to your port:

```bash
npx add-mcp@2.4.0 https://getport.app/mcp --name port -g
```

The first time an agent uses it, it opens getport.app: sign in, press Allow, and it is connected.
No key. Then teach them how Port_ answers:

```bash
npx skills add FitzDegenhub/port-skills -g
```

claude.ai, Claude Desktop and ChatGPT: add a custom connector with the address
`https://getport.app/mcp` and sign in when it asks.

Or both at once as a plugin. Claude Code:

```text
/plugin marketplace add FitzDegenhub/port-skills
/plugin install port@port
```

Codex:

```bash
codex plugin marketplace add FitzDegenhub/port-skills
codex plugin add port@port
```

It needs a Port_ Pro account, which is free during the open beta. Scripts that call the REST
API use a key instead, made under Settings, API access; `port-api/SKILL.md` has how.

## What is in here

| Path | What it is |
| ---- | ---------- |
| `port-api/` | The skill for the REST API, with every endpoint in `references/endpoints.md` |
| `port-mcp/` | The skill for the MCP server at `https://getport.app/mcp` and how to connect it |
| `.claude-plugin/` | The Claude Code marketplace and plugin manifest |
| `.mcp.json` | The MCP server the Claude Code plugin registers; it signs you in the first time |
| `.agents/plugins/`, `.codex-plugin/` | The Codex marketplace and plugin manifest |

## How it is made

This repository is published from the Port_ codebase and is overwritten on every change, so a
pull request here would be lost. The endpoint reference is generated from the same table the API
itself is built from. Docs: [docs.getport.app](https://docs.getport.app/api/).
