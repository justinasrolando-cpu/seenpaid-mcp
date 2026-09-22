# seenpaid — MCP server

Connect your AI agent to **[seenpaid](https://seenpaid.com)** and ask the question every poster
actually cares about: **"which of my posts made money?"**

seenpaid schedules social posts to 21 platforms (X, LinkedIn, Bluesky, Threads, TikTok,
Mastodon, Discord, and more) and attributes **real revenue** — via Stripe — back to the individual post that
drove each sale. This repo is the open client + docs for that hosted service.

> **Open client to a paid service.** seenpaid's cloud (the revenue-matching engine + dashboard) is
> proprietary. This repo is the connector, the [`server.json`](./server.json) registry manifest, and
> a [Claude Skill](./skills/seenpaid/SKILL.md) so agents can use it. Free trial at
> [seenpaid.com](https://seenpaid.com).

## Tools

50 tools: post, schedule, validate a caption, read what is queued, and see which posts made money.
The full list is in [`skills/seenpaid/references/tools.md`](./skills/seenpaid/references/tools.md).

## Agent Skill

[`skills/seenpaid`](./skills/seenpaid/SKILL.md) is an [Agent Skill](https://agentskills.io) that tells an
agent how to use these tools safely (always name the platforms, confirm before publishing, read back
what was queued). Install it with:

```bash
npx skills add justinasrolando-cpu/seenpaid-mcp
```

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
