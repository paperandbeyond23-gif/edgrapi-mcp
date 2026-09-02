# Edgrapi MCP — SEC EDGAR filings for your AI agent

[![Website](https://img.shields.io/badge/site-edgrapi.com-1C6E5A)](https://edgrapi.com)
[![MCP](https://img.shields.io/badge/MCP-streamable--http-6E56CF)](https://api.edgrapi.com/mcp)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

> **This repo is a manifest mirror, not the canonical listing.**
> Edgrapi's MCP server is published as
> [`paperandbeyond23-gif/edgrapi-skills`](https://github.com/paperandbeyond23-gif/edgrapi-skills) —
> that's the repo carried by the [official MCP registry](https://registry.modelcontextprotocol.io)
> and by Glama. Link that one. The MCP endpoint below is the same either way.

**Real SEC filings as clean JSON, in one call — insider trades, 8-Ks, 13F holdings, and >5% stakes.**

Edgrapi is a hosted [Model Context Protocol](https://modelcontextprotocol.io) server (and plain
REST API) for [SEC EDGAR](https://www.sec.gov/edgar) data. The SEC's data is free; its XBRL is the
pain. Edgrapi does the parsing so your agent doesn't have to. Point Claude, Cursor, or Cline at one
URL and get real filings mid-conversation — no XBRL, no CIK lookups, no scraping.

- **Endpoint:** `https://api.edgrapi.com/mcp` (streamable-http)
- **Auth:** `Authorization: Bearer edgr_<key>` or OAuth 2.1 (the server is its own provider)
- **Free tier:** 100 credits / month, no card — [get a key](https://edgrapi.com/app)

## Tools

The live tool list is served by the server itself, so it never goes stale here:

```bash
curl -s -X POST https://api.edgrapi.com/mcp   -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream'   -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

Also in the [server card](https://api.edgrapi.com/.well-known/mcp/server-card.json), which is
generated from the same source.

Broadly: Form 4 insider trades (including market-wide buys and cluster-buy detection), typed 8-K
material events, 13F fund holdings, 13D/13G >5% stakes, Form D raises, subsidiaries, normalized
fundamentals and ratios, XBRL-to-JSON, shares outstanding and public float, company profiles,
filings, 10-K/10-Q sections, entity resolution from ticker/CIK/CUSIP, and full-text search across
every filing since 2001.

Most tools take a US-listed ticker. `get_holdings` takes a fund instead (a name like `berkshire`, a
CIK, or a filer ticker like `BRK-B`), and `get_activist` takes either a ticker or `latest` for the
market-wide feed.

**It's the parsing that's the product.** Raw EDGAR will happily mislead you: funds report one
position across sub-managers so the 13F table double-counts unless you aggregate by CUSIP; a 13F PUT
is a bearish bet, not a holding; pre-2023 filings report value in thousands and newer ones in whole
dollars. `get_holdings` handles all three and labels every position new / added / reduced / exited
against the prior quarter. `get_insider` separates code P (an insider spending their own money) from
pre-scheduled 10b5-1 sales that carry little signal.

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

Then ask your agent: *"What insider buying has happened at NVDA lately?"*, *"What did Berkshire add
and exit last quarter?"*, or *"Who just crossed 5% in a US company with activist intent?"*

## REST API (no MCP client needed)

```bash
curl "https://api.edgrapi.com/v1/insider/AAPL" -H "X-API-Key: edgr_YOUR_KEY"
curl "https://api.edgrapi.com/v1/holdings/berkshire" -H "X-API-Key: edgr_YOUR_KEY"
curl "https://api.edgrapi.com/v1/activist/latest?activist_only=true" -H "X-API-Key: edgr_YOUR_KEY"
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
