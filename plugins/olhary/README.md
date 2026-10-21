# Olhary

Search Brazil's public procurement data from your agent, and check the companies in it. The plugin
connects your agent to `https://api.olhary.ai/mcp` and teaches it how to use the tools. The data
and its field names are in Portuguese.

## What's inside

- A skill that picks the right tool, explains the filters, reads each answer and says what to do
  on a refusal.
- The remote MCP server at `https://api.olhary.ai/mcp`. Nothing runs on your machine.
- Manifests for Claude Code and Agent Plugins (ChatGPT, Codex and Cursor).

The server has eight tools. All of them only read public data. None looks up people or CPFs, reads
your private Olhary data or changes anything.

| Tool                                  | What it reads                                                                              |
| ------------------------------------- | ------------------------------------------------------------------------------------------ |
| `search_procurements`                 | Tenders, price-register agreements (atas) and contracts from PNCP, by text and filters.    |
| `get_company_sanctions`               | A company's records in CEIS, CNEP, CEPIM and leniency agreements, with each source's date. |
| `get_company_procurement_performance` | A supplier's recorded wins, awarded values and totals by period, from PNCP.                |
| `search_catalog_items`                | CATMAT and CATSER item codes, by description or code.                                      |
| `search_legislation`                  | Passages of federal legislation, with their law and label.                                 |
| `search_municipalities`               | Municipalities by name and state, with their IBGE code.                                    |
| `get_municipality`                    | A municipality's population and GDP, from its IBGE code.                                   |
| `get_cnae`                            | What a CNAE business activity code covers.                                                 |

Sources can be incomplete or late. A company with no sanctions in the answer is not proof that it
is clear: the answer says whether each register was verified, and the skill tells the agent to
read that first.

## Install

In Claude Code:

```sh
claude plugin marketplace add olhary-ai/plugins
claude plugin install olhary@olhary-ai
```

In Codex:

```sh
codex plugin marketplace add olhary-ai/plugins
codex plugin add olhary@olhary-ai
```

## Sign in

In Claude Code, type `/mcp`, choose olhary and Authenticate. In Codex 0.147 or later, run
`codex mcp login olhary`. The browser opens olhary.ai. Sign in to your Olhary account, check the
app's name and the address it returns to, and press Connect (Conectar in Portuguese).

The app gets its own key (`lct_mcp_...`), named after the app. It appears in your account under
Settings > API keys (Configurações > Chaves de API in Portuguese), and counts toward the limit of
10 keys. Unlike a key you make there, it opens only these eight lookups. Revoke it there to
disconnect at once. No key needs to be pasted into this plugin's files.

Each lookup counts toward your account's per-minute request limit.

On a machine with no browser, the skill uses a device code with `client_id=olhary-cli`. Open the
link on a device where you are signed in, check the 8-letter code and approve it. The code expires
in 10 minutes.

## Your data

The text of a `search_procurements` query is sent to Google's Gemini API, which turns it into
numbers to find similar results. Search with filters only to send no text. The other seven tools
send no text to it. Olhary also logs each lookup: the route (which can hold the CNPJ or IBGE code
you searched), the time, your account and the key.

The privacy policy has the rest, in section 5.1 (connected AI apps) and section 5.2 (where the text
goes).

## Links

- [MCP setup guide](https://olhary.ai/docs/mcp)
- [Privacy policy](https://olhary.ai/privacidade)
- [Terms](https://olhary.ai/termos)
- What to try, and what should be refused: [TESTING.md](TESTING.md)
- Help: [info@olhary.ai](mailto:info@olhary.ai) or [olhary.ai/suporte](https://olhary.ai/suporte)
