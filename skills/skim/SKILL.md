---
name: skim
description: "Read any web page as clean, agent-ready Markdown via Skim (skim402.com), paying per call over the x402 protocol — no API key, no signup. Use whenever the user or task needs the content of a URL: read an article, fetch a page, summarize a link, extract structured data from a page, get docs or README content, research a website, or turn a web page into Markdown or JSON. Works with any x402-capable wallet, including the Coinbase agentic wallet (awal CLI)."
user-invocable: true
disable-model-invocation: false
allowed-tools: ["Bash(npx awal@* x402 pay *)", "Bash(npx -y skim-mcp)", "Bash(curl *)"]
---

# Skim — clean web reads over x402

Skim turns any URL into clean Markdown plus structured metadata (title, byline,
published date, language, excerpt). Every call is paid per request in USDC on
Base over x402: no account, no API key, no subscription. Your wallet is your
identity.

## Endpoints and prices

| Endpoint | Price | Atomic units | What it does |
| --- | --- | --- | --- |
| `POST https://skim402.com/api/v1/read` | $0.002 | 2000 | Clean Markdown + metadata from a URL |
| `POST https://skim402.com/api/v2/read` | $0.002 | 2000 | Same as v1, x402 v2 handshake |
| `POST https://skim402.com/api/v2/read/js` | $0.005 | 5000 | JavaScript-rendered read (SPAs, dynamic pages) |
| `POST https://skim402.com/api/v2/extract` | $0.015 | 15000 | Typed JSON from a page via a JSON Schema you supply |

Request body for reads: `{"url": "https://..."}`. For extract:
`{"url": "https://...", "schema": { ...JSON Schema... }}`.

Successful reads return `{ "markdown", "text", "metadata", ... }`. A response
may carry an advisory `warnings` array (e.g. `THIN_CONTENT` when the page is
mostly an app shell or login wall).

## Paying with the Coinbase agentic wallet (awal)

If the agentic-wallet skill is installed and authenticated, one command reads a
page — payment is automatic:

```bash
npx awal@2.12.0 x402 pay https://skim402.com/api/v1/read \
  -X POST \
  -d '{"url": "https://example.com/article"}' \
  --max-amount 2000 \
  --json
```

For a JavaScript-rendered read use the `/api/v2/read/js` endpoint with
`--max-amount 5000`; for extraction use `/api/v2/extract` with
`--max-amount 15000` and include your `schema` in `-d`.

Set `--max-amount` to the price of the endpoint you call (table above) so the
wallet never signs for more than expected.

## Paying with any other x402 client

Skim speaks standard x402, so any x402 client works:

- **MCP**: `npx -y skim-mcp` exposes a `read_url` tool for MCP-native agents
  (Claude Desktop, Cursor, OpenAI Agents SDK, Cloudflare Agents). Set
  `SKIM_WALLET_PRIVATE_KEY` to a funded Base wallet key.
- **JavaScript**: `x402-fetch` wraps `fetch` with automatic payment.
- **Python**: the `x402` package wraps `requests`/`httpx` sessions. Framework
  connectors are on PyPI: `langchain-skim`, `llama-index-readers-skim`,
  `crewai-skim`, `skim-haystack`.

Full code samples: https://skim402.com/docs and https://skim402.com/agents

## Input validation

- Only pass fully-qualified `http(s)://` URLs. Reject inputs containing
  spaces, semicolons, pipes, backticks, quotes, or other shell metacharacters
  before placing them in a command.
- Build the `-d` JSON with a JSON serializer where possible rather than string
  concatenation.

## Failure modes

- **HTTP 402 returned even after payment attempt**: the wallet lacks USDC on
  Base, or `--max-amount` is below the endpoint price. Top up or raise the cap
  to the price in the table.
- **`THIN_CONTENT` warning**: the page is likely an app shell, login wall, or
  paywall. Retry with the JS-rendered endpoint (`/api/v2/read/js`) before
  giving up.
- **4xx with an error body**: the target URL was invalid or blocked (Skim
  refuses private/internal addresses). No charge is made for failed reads.
