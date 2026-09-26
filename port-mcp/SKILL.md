---
name: port-mcp
description: >-
  Use when the person asks about their own Port_ port and the Port_ MCP server is connected,
  or when they want to connect it: net worth, holdings, DeFi, perps, prediction markets, NFTs,
  PnL, what moved, the daily briefing, alerts. The server is https://getport.app/mcp, read
  only, over the person's own API key.

  TRIGGERS: my port, my net worth, my holdings, my PnL, what did I lose on, what moved in my
  wallet, Port_, connect Port_, Port_ MCP
metadata:
  author: getport
  version: "1.0"
---

# Port_ MCP

[Port_](https://getport.app) is a read-only crypto portfolio tracker. Its MCP server answers
questions about one account's own port, the one the key belongs to, with the same reads and the
same caveats as the app. It cannot move funds, sign anything or change a setting.

|             |                                            |
| ----------- | ------------------------------------------ |
| URL         | `https://getport.app/mcp`                  |
| Transport   | Streamable HTTP, stateless                 |
| Auth        | `Authorization: Bearer $PORT_API_KEY`      |
| Limits      | 60 calls a minute, 5000 a day              |

## Getting a key

The person makes one under Settings, API access, at
[getport.app/settings/api](https://getport.app/settings/api). It needs a Pro account, starts
`port_` and is shown once. Keep it in the `PORT_API_KEY` environment variable. Never ask for it
in the chat and never print it.

## Connecting

Claude Code, with the plugin, which carries this server and both skills. It asks for the key
when the plugin is enabled and keeps it in the system's credential store, not in a file:

```text
/plugin marketplace add FitzDegenhub/port-skills
/plugin install port@port
```

If the key was skipped at install, `/plugin configure port@port` asks again. The `port-api`
skill's curl calls still read `PORT_API_KEY` from the shell, so set that too if you want them.

Claude Code, by hand:

```bash
claude mcp add --transport http port https://getport.app/mcp --header "Authorization: Bearer $PORT_API_KEY"
```

Codex, in `~/.codex/config.toml`:

```toml
[mcp_servers.port]
url = "https://getport.app/mcp"
bearer_token_env_var = "PORT_API_KEY"
```

Or the plugin, which carries this server and both skills and reads the same `PORT_API_KEY`
from the environment:

```bash
codex plugin marketplace add FitzDegenhub/port-skills
codex plugin add port@port
```

Cursor, in `.cursor/mcp.json`:

```json
{ "mcpServers": { "port": { "url": "https://getport.app/mcp", "headers": { "Authorization": "Bearer ${env:PORT_API_KEY}" } } } }
```

VS Code, in `.vscode/mcp.json`:

```json
{ "servers": { "port": { "type": "http", "url": "https://getport.app/mcp", "headers": { "Authorization": "Bearer ${env:PORT_API_KEY}" } } } }
```

claude.ai, Claude Desktop and ChatGPT connect through sign-in rather than a key, and that is not
available yet.

## Which tool answers what

| The person asks | Tool |
| --------------- | ---- |
| What is my port worth, by wallet, and what is not counted | `port_overview` |
| What do I hold, how much, what is each row priced at | `port_holdings` (`wallet`, `chain`, `hidden`, `cursor`) |
| What do I have in DeFi | `port_positions` |
| What perps do I have open | `port_perps` |
| What prediction markets am I in | `port_predictions` |
| What NFTs do I hold and at what floor | `port_nfts` |
| What is my realised PnL and cost basis | `port_pnl` (`method`: fifo, lifo, hifo, average) |
| What moved in my wallets | `port_activity` (`cursor`) |
| What does my briefing say | `port_briefing` (`day`: YYYY-MM-DD) |
| Which alerts fired | `port_alerts` |
| Which wallets are on the account | `port_wallets` |
| Read my wallets again now | `port_refresh`, listed only when the key may refresh |

Each tool answers with a text block and `structuredContent`. The text leads with what is not
counted, then one line of what came back, then how old it is. `structuredContent` is the same
`data` the REST API answers, field for field.

## Reading the answers

These are the product's rules, and they are what keeps a figure honest:

1. **Quote the caveat and the age with any figure.** The text block's first sentence is the
   product's own line on what the headline leaves out. Repeat it, with the "as of" time.
2. **Never sum rows whose `counted` is false.** A price below the confidence bar is shown on
   its row and never added to anything.
3. **`port_overview` is the total. Never add up `port_holdings` rows yourself.** The headline
   carries DeFi, venue balances and perp equity that no holdings row does.
4. **A watched wallet is left out.** The overview, holdings, DeFi, perps, predictions, NFTs and
   activity cover the wallets the person owns and has not excluded, as the screens do. PnL
   covers every wallet on the account. `port_wallets` lists every wallet, watched and excluded
   ones marked.
5. **Keep calling `port_activity` with the cursor until it is null.** A page can be empty with
   a cursor that is not. The same for `port_holdings`.
6. **NFT floors and predictions are never in net worth.** Do not add them in.
7. **A PnL marked incomplete is not final.** Say so before quoting a realised figure.
8. **"Net worth not quoted"** means the sum rests on a price the product cannot vouch for. Do
   not work one out from the rows.

An error comes back as an `isError` result with a message written for a person: quote it. "No
briefing" is the normal answer for a new account, not a fault. A rate limit means wait, not
retry in a loop.

## Rules

- Never print the key or put it in a prompt, a URL or a file.
- Call `port_refresh` only when asked. It spends one of 20 a day, and the answer is the queue,
  not the new figures.
- Join tokens by contract address, never by ticker.

## Links

- Docs: [getport.app/docs](https://getport.app/docs)
- Make a key: [getport.app/settings/api](https://getport.app/settings/api)
- The REST twin of every tool: the `port-api` skill in this repository
