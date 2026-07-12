# Skim Agent Skill

An [Agent Skill](https://agentskills.io) that gives AI agents clean web
reading via [Skim](https://skim402.com) — the x402-native clean reader API.
Send a URL, get back agent-ready Markdown plus structured metadata. Paid per
call in USDC on Base over the x402 protocol: no API key, no signup.

## What it does

A single skill — [`skim`](./skills/skim/SKILL.md) — that teaches the agent to:

| Capability | Price |
| --- | --- |
| Read any URL as clean Markdown + metadata | $0.002 |
| JavaScript-rendered reads for SPAs and dynamic pages | $0.005 |
| Extract typed JSON from a page via a JSON Schema | $0.015 |

Payment works with the [Coinbase agentic wallet](https://github.com/coinbase/agentic-wallet-skills)
(`awal x402 pay`) or any other x402 client (MCP server, `x402-fetch`, the
Python `x402` package).

## Installation

Install with [Vercel's Skills CLI](https://skills.sh):

```bash
npx skills add JessieJanie/skim-agent-skills
```

## Usage

Skills are automatically available once installed. The agent will use Skim
when it needs the content of a web page.

**Examples:**

```text
Read https://example.com/article and summarize it
```

```text
Extract the product name and price from this page as JSON
```

## Related

- Docs and code samples: https://skim402.com/docs
- Framework connectors on PyPI: `langchain-skim`, `llama-index-readers-skim`, `crewai-skim`, `skim-haystack`
- MCP server on npm: `skim-mcp`

## License

MIT
