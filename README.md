# Perflo

Pay for data and move money from your agent.

Perflo gives an assistant two things: a **spending rail** that buys information from
550+ paid providers (research on a person or company, web and social data, images,
gift cards), and a **money rail** that moves the user's own funds — sending to a
person, withdrawing to a wallet, earning, and more.

The user owns the wallet. The agent never holds keys, and every paid call runs inside
limits the user sets once and can revoke at any time.

## Install

Perflo is one remote MCP server, `https://mcp.perflo.ai/mcp`. How you add it depends
on the host. **You do not need this repository to use Perflo in Claude** — the
connector is the server, and this repo only adds the two skills on top of it.

### Claude (connector)

Add it as a custom connector in Settings → Connectors, using the server URL above.
Once the directory listing is approved it will also be searchable by name, with no URL
to paste.

### Claude Code (plugin)

This repository is a Claude Code plugin: it installs the same server *and* the two
skills below.

```bash
claude plugin marketplace add perflo-ai/perflo-plugin
claude plugin install perflo@perflo-ai
```

Until the plugin is listed, install it straight from a clone:

```bash
git clone https://github.com/perflo-ai/perflo-plugin
claude --plugin-dir ./perflo-plugin
```

### Cursor

Add it from the Cursor marketplace, or point at the server directly:

```json
{
  "mcpServers": {
    "perflo": {
      "url": "https://mcp.perflo.ai/mcp"
    }
  }
}
```

### Repository layout

The two manifest pairs exist because the two hosts read different filenames. They are
kept in agreement by CI, so edit both or neither:

| File | Read by |
| :--- | :--- |
| `plugin.json` | Cursor |
| `mcp.json` | Cursor |
| `.claude-plugin/plugin.json` | Claude Code |
| `.mcp.json` | Claude Code |

## Signing in

The server speaks OAuth 2.1 with dynamic client registration, so there is **no API key
to paste and nothing secret in this repo**. On first use your client shows a connect
card; approving it links the session to your Perflo account.

You will need an account at [app.perflo.ai](https://app.perflo.ai) with spending set
up before a paid call will go through.

## What it can do

**Buy information.** `spend` takes a plain-English task, picks a provider, pays for the
call and returns the result. Slow calls hand back a collection id you poll with
`get_task_result` — free, and safe to call repeatedly.

**Move money.** Pay a saved recipient, send to an email address, withdraw to a wallet,
move funds between your own accounts, deposit into earn vaults.

**Read.** Balances, portfolio, transaction history, spending headroom and limits.

## Money safety

This plugin can spend real money and move real funds. What protects you:

- **Limits you set.** Per-call and per-window caps live on your account, not in the
  agent. `get_spending_status` shows the current headroom.
- **A kill switch.** `stop_spending` revokes the spending permission immediately. It
  is reversible from the app.
- **Your client's approval prompt.** Money-moving tools are marked destructive, so your
  MCP client prompts before they run.
- **Free refusals.** A request that does not match a provider's contract is rejected
  before any payment, not after.
- **Read, don't retry.** `get_transactions` is the way to find out whether a charge
  landed. The bundled skills tell the agent this explicitly, because re-running a paid
  call to check is how a charge happens twice.

## Skills

Two skills ship with the plugin and load automatically:

- **`paid-data-calls`** — how to call `spend`, collect a slow result without paying
  twice, and check whether a charge landed.
- **`moving-money`** — what executes immediately, which features need switching on,
  and how to talk about money without jargon.

## Data handling

Requests you make are sent to the provider chosen for that task, and the result is
returned to your session. Perflo stores transaction records for your account so
charges can be audited. Results of slow calls are held briefly so they can be
collected, then expire.

## Privacy Policy

Perflo's privacy policy is at **https://perflo.ai/privacy**. It covers what is
collected, how it is used and stored, who it is disclosed to, how long it is kept and
how to contact us. Clause 4 is the one to read first: it sets out the commitments made
about Agent Data, including that it is not trained on and not read.

The terms are at https://perflo.ai/terms.

## Links

- App — https://app.perflo.ai
- Docs — https://docs.perflo.ai
- Privacy policy — https://perflo.ai/privacy
- Terms — https://perflo.ai/terms
- Support — info@perflo.ai
- Server — `https://mcp.perflo.ai/mcp`

## License

MIT
