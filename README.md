# mcp-unwrangle

Unwrangle MCP — cross-retailer product + reviews data (unwrangle.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `unwrangle_product` | Product detail from a retailer (Amazon/Target/Best Buy/Home Depot/Walmart/…). Returns title, brand, price, rating, availability. Pass the product `url` (works for any retailer), or for Amazon pass `id`/`asin`. Example: unwrangle_product({ retailer: "amazon", url: "https://www.amazon.com/dp/B07ZPKN6YR", _apiKey: "your-key" }) |
| `unwrangle_reviews` | Reviews for a product on a retailer (Amazon/Target/Best Buy/…). Returns reviewer name, rating, title, text, date, verified-purchase flag. Pass the product `url` (or Amazon `id`/`asin`) and optional `page`. Reviews cost more credits than product/search. Example: unwrangle_reviews({ retailer: "amazon", url: "https://www.amazon.com/dp/B07ZPKN6YR", page: 1, _apiKey: "your-key" }) |
| `unwrangle_search` | Search a retailer (Amazon/Target/Best Buy/Home Depot/…) by keyword. Returns product name, brand, price, rating, url, id. Example: unwrangle_search({ retailer: "amazon", search: "wireless earbuds", page: 1, _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "unwrangle": {
      "url": "https://gateway.pipeworx.io/unwrangle/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/unwrangle/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/unwrangle_product`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "unwrangle": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-unwrangle"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-unwrangle
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Unwrangle data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
