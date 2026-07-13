# seenpaid — MCP server

Connect your AI agent to **[seenpaid](https://seenpaid.com)** and ask the question every poster
actually cares about: **"which of my posts made money?"**

seenpaid schedules social posts to 25+ platforms (X, LinkedIn, Instagram, Facebook, TikTok,
Bluesky, and more) and attributes **real revenue** — via Stripe — back to the individual post that
drove each sale. This repo is the open client + docs for that hosted service.

> **Open client to a paid service.** seenpaid's cloud (the revenue-matching engine + dashboard) is
> proprietary. This repo is the connector, the [`server.json`](./server.json) registry manifest, and
> a [Claude Skill](./skills/seenpaid/SKILL.md) so agents can use it. Free trial at
> [seenpaid.com](https://seenpaid.com).

## Tools

| tool | description |
|------|-------------|
| `list_accounts` | List connected social accounts (platform, handle, id, status). |
| `list_posts` | List recent posts with status, schedule time, and target platforms. |
| `schedule_post` | Schedule or immediately publish a post to one or more platforms. |
| `get_analytics` | Revenue attribution — clicks, sales, revenue, with per-platform / country / device breakdowns (7, 30, or 90 days). |
| `get_top_posts` | Your posts ranked by revenue earned — "which post made the most money." |

## Setup

1. Sign up at **[seenpaid.com](https://seenpaid.com)**, open **AI Agents**, and create an API key
   (starts with `sp_`).
2. Add the remote MCP server to your client (Claude Desktop, Cursor, Cline, etc.):

```json
{
  "mcpServers": {
    "seenpaid": {
      "url": "https://go.seenpaid.com/mcp",
      "headers": { "Authorization": "Bearer sp_YOUR_KEY" }
    }
  }
}
```

3. Ask your agent:

> "Which of my posts made the most money last month?"
> "Schedule this to X and LinkedIn for 9am tomorrow."
> "How much revenue did my posts drive in the last 30 days?"

See [`examples/claude_desktop_config.json`](./examples/claude_desktop_config.json) for a full config.

## How attribution works

Every link seenpaid posts gets a tracked short URL. When a click leads to a Stripe sale, seenpaid
matches that sale back to the exact post — so revenue numbers are real, not estimated. If
`get_analytics` / `get_top_posts` return zeros, the account probably hasn't connected Stripe or
published a tracked post yet.

## Links

- Website & sign-up: https://seenpaid.com
- MCP endpoint: `https://go.seenpaid.com/mcp`

## License

MIT for this client + docs (see [LICENSE](./LICENSE)). The seenpaid service itself is proprietary.
