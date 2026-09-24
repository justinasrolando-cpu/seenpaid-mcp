# seenpaid — MCP server

**seenpaid is an agentic social media scheduler.** Connect Claude, ChatGPT, Cursor or any MCP
client and your agent can schedule and publish to 21 platforms (X, LinkedIn, Bluesky, Threads,
TikTok, Mastodon, Discord and more), check each caption against every platform's rules, and read
back what is queued. It is listed in ChatGPT's plugin directory (approved 24 September 2026).

Optionally, connect your own Stripe (read-only) and seenpaid matches sales back to the post that
drove them, so your agent can answer **"which of my posts made money?"**

> **Open client to a paid service.** seenpaid's cloud (scheduler, revenue matching, dashboard) is
> proprietary. This repo is the connector, the [`server.json`](./server.json) registry manifest, and
> a [Claude Skill](./skills/seenpaid/SKILL.md) so agents can use it. 7-day trial at
> [seenpaid.com](https://seenpaid.com) (card required, no free plan).

## Tools

50 tools: post, schedule, validate a caption, read what is queued, and see which posts made money.
The full list is in [`skills/seenpaid/references/tools.md`](./skills/seenpaid/references/tools.md).
Four walk-through examples (schedule to X + LinkedIn, plan a week with `bulk_schedule`, "which posts
made money", and connecting an account) are in [`examples/`](./examples/).

## Agent Skill

[`skills/seenpaid`](./skills/seenpaid/SKILL.md) is an [Agent Skill](https://agentskills.io) that tells an
agent how to use these tools safely (always name the platforms, confirm before publishing, read back
what was queued).

This repo is also packaged as a **plugin** — the skill and the hosted MCP server together — for Claude
Code, the Claude apps, Cursor, and Grok, plus a Gemini CLI extension. Pick whichever install path matches
your client.

## Install

### Claude Code

```bash
/plugin marketplace add justinasrolando-cpu/seenpaid-mcp
/plugin install seenpaid@seenpaid
```

This installs the [`skills/seenpaid`](./skills/seenpaid/SKILL.md) skill and connects the hosted MCP
server (declared in [`.mcp.json`](./.mcp.json)) in one step. The server supports OAuth, so the first
tool call opens a browser to sign in to seenpaid — no key to paste.

### Claude apps (claude.ai / Claude Desktop) — custom connector

Settings → Connectors → Add custom connector → URL `https://api.seenpaid.com/mcp` → sign in when
prompted (OAuth). See [`examples/claude_desktop_config.json`](./examples/claude_desktop_config.json)
for the config-file form (Claude Desktop, or any MCP-config client) using a static `sp_…` API key
instead.

### Cursor

Point Cursor at [`.cursor-plugin/`](./.cursor-plugin) (`plugin.json` + `marketplace.json` +
`mcp.json`) — same shape as `.claude-plugin/`. Or add the server directly in Cursor Settings → MCP
with the URL and header from `examples/claude_desktop_config.json` if you're using a static `sp_…`
key instead of OAuth.

### Grok

Point Grok at [`.grok-plugin/`](./.grok-plugin) — same three files, Grok's marketplace shape.

### Gemini CLI

```bash
gemini extensions install https://github.com/justinasrolando-cpu/seenpaid-mcp.git
```

Uses [`gemini-extension.json`](./gemini-extension.json) at the repo root, which bundles the skill and
the hosted MCP server (OAuth on first connection).

### `npx skills add` (skill only, any client that reads Agent Skills)

```bash
npx skills add justinasrolando-cpu/seenpaid-mcp
```

Installs just [`skills/seenpaid`](./skills/seenpaid/SKILL.md) — pair it with a manual MCP server
connection (see above) if your client doesn't read `.mcp.json`.

## Setup

1. Sign up at **[seenpaid.com](https://seenpaid.com)**, open **AI Agents**, and create an API key
   (starts with `sp_`) if your client needs one (config-file clients; UI clients use OAuth instead).
2. Connect the remote MCP server with whichever install path above matches your client.
3. Ask your agent:

> "Which of my posts made the most money last month?"
> "Schedule this to X and LinkedIn for 9am tomorrow."
> "How much revenue did my posts drive in the last 30 days?"

## How attribution works

Every link seenpaid posts gets a tracked short URL. When a click leads to a Stripe sale, seenpaid
matches that sale back to the exact post — so revenue numbers are real, not estimated. If
`get_analytics` / `get_top_posts` return zeros, the account probably hasn't connected Stripe or
published a tracked post yet.

## Links

- Website & sign-up: https://seenpaid.com
- MCP endpoint: `https://api.seenpaid.com/mcp`

## License

MIT for this client + docs (see [LICENSE](./LICENSE)). The seenpaid service itself is proprietary.
