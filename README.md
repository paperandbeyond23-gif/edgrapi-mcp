# Edgrapi MCP — SEC EDGAR filings for your AI agent

[![Website](https://img.shields.io/badge/site-edgrapi.com-1C6E5A)](https://edgrapi.com)
[![MCP](https://img.shields.io/badge/MCP-streamable--http-6E56CF)](https://api.edgrapi.com/mcp)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

**Real SEC filings as clean JSON, in one call — insider trades, 8-Ks, 13Fs, and fundamentals.**

Edgrapi is a hosted [Model Context Protocol](https://modelcontextprotocol.io) server (and plain
REST API) for [SEC EDGAR](https://www.sec.gov/edgar) data. The SEC's data is free; its XBRL is the
pain. Edgrapi does the parsing so your agent doesn't have to. Point Claude, Cursor, or Cline at one
URL and get real filings mid-conversation — no XBRL, no CIK lookups, no scraping.

- **Endpoint:** `https://api.edgrapi.com/mcp` (streamable-http)
- **Auth:** `Authorization: Bearer edgr_<key>` or OAuth 2.1 (the server is its own provider)
- **Free tier:** 100 credits / month, no card — [get a key](https://edgrapi.com/app)

## Tools

| Tool | What it returns |
|---|---|
| `get_insider` | Form 4 insider trades (open-market buys/sells) for a ticker |
| `get_events` | 8-K material events (earnings, M&A, officer changes, more) |
| `get_fundamentals` | Income statement, balance sheet, cash flow — normalized |
| `get_ratios` | 14 computed ratios (margins, ROE/ROA, leverage, liquidity) |
| `get_company` | Profile: CIK, name, SIC, exchange, fiscal-year end |
| `get_filings` | Recent 10-K / 10-Q / 8-K filings with links |
| `get_sections` | 10-K / 10-Q narrative sections as text (e.g. risk factors) |

Every tool works on any US-listed ticker, sourced from public SEC EDGAR.

## Quickstart (MCP client)

Add to your Claude Desktop / Cursor / Cline MCP config:

```json
{
  "mcpServers": {
    "edgrapi": {
      "url": "https://api.edgrapi.com/mcp",
      "headers": { "Authorization": "Bearer edgr_YOUR_KEY" }
    }
  }
}
```

Or install the agent skill bundle:

```bash
npx skills add paperandbeyond23-gif/edgrapi-skills --skill edgrapi-full
export EDGRAPI_KEY=edgr_YOUR_KEY
```

Then ask your agent: *"What insider buying has happened at NVDA lately?"* or *"Pull Apple's last
four years of revenue and net margin."*

## REST API (no MCP client needed)

```bash
curl "https://api.edgrapi.com/v1/insider/AAPL" -H "X-API-Key: edgr_YOUR_KEY"
curl "https://api.edgrapi.com/v1/fundamentals/AAPL?period=annual&limit=4" -H "X-API-Key: edgr_YOUR_KEY"
```

Auth accepts `X-API-Key: edgr_...`, `Authorization: Bearer edgr_...`, or `?api_key=`. Full docs and
OpenAPI at [edgrapi.com/docs](https://edgrapi.com/docs) and
[api.edgrapi.com/openapi.json](https://api.edgrapi.com/openapi.json).

## Links

- Site: https://edgrapi.com
- Agents & MCP guide: https://edgrapi.com/agents
- Skills repo: https://github.com/paperandbeyond23-gif/edgrapi-skills
- MCP server card: https://api.edgrapi.com/.well-known/mcp/server-card.json
- llms.txt: https://edgrapi.com/llms.txt

## License

MIT — see [LICENSE](LICENSE). Data sourced from public SEC EDGAR. Not investment advice.
