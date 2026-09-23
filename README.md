# mcp-electricity-maps

Electricity Maps MCP — real-time electricity-grid carbon intensity + power

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1663+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `electricitymaps_carbon_intensity` | Carbon intensity (gCO2/kWh) for a grid zone now — the live climate cost of a kWh drawn from the local grid. Pass a zone code (e.g. "US-CAL-CISO", "DE", "FR", "GB") or lat+lon. Example: electricitymaps_carbon_intensity({ zone: "DE", _apiKey: "your-key" }) |
| `electricitymaps_power_breakdown` | Power generation mix for a zone — the latest breakdown of electricity by source (nuclear, wind, solar, hydro, coal, gas, ...) plus fossil-free and renewable percentages. Pass a zone code (e.g. "US-CAL-CISO", "DE", "FR", "GB") or lat+lon. Example: electricitymaps_power_breakdown({ zone: "FR", _apiKey: "your-key" }) |
| `electricitymaps_carbon_forecast` | Carbon-intensity forecast for a zone — upcoming hourly gCO2/kWh predictions, useful for scheduling loads when the grid is cleanest. Requires a paid Electricity Maps plan. Pass a zone code (e.g. "US-CAL-CISO", "DE", "FR", "GB"). Example: electricitymaps_carbon_forecast({ zone: "GB", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "electricity-maps": {
      "url": "https://gateway.pipeworx.io/electricity-maps/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/electricity-maps/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1663+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/electricitymaps_carbon_intensity`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "electricity-maps": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-electricity-maps"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-electricity-maps
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Electricity Maps data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
