---
name: seenpaid
description: >-
  Find out which social posts made money, and schedule new ones. Use when the user asks which of
  their posts made money / drove revenue / sales, wants revenue attribution for their social content,
  wants to know what to post more of, or wants to schedule or publish posts across social platforms.
  Requires the seenpaid MCP server connected at https://go.seenpaid.com/mcp with a Bearer API key.
---

# seenpaid — revenue attribution for social posts

seenpaid tells you **which of your social posts actually made money** (matched to real Stripe
sales) and schedules new posts across 25+ platforms. Use its MCP tools to answer money questions
and to publish.

## When to use this skill
- "Which of my posts made the most money?" → `get_top_posts`
- "How much revenue did my posts drive last month?" → `get_analytics` (days: 7 | 30 | 90)
- "Which platform makes me the most money?" → `get_analytics`, read `by_platform`
- "Schedule a post to X and LinkedIn" → `schedule_post`
- "What accounts are connected?" → `list_accounts`
- "Show my recent / scheduled posts" → `list_posts`

## Tools
| tool | input | returns |
|------|-------|---------|
| `list_accounts` | — | connected accounts: platform, name, id, status |
| `list_posts` | `limit?` | recent posts: caption, status, schedule, platforms |
| `schedule_post` | `caption`, `platforms?` or `account_ids?`, `schedule_for?` | posts now (omit `schedule_for`) or schedules it |
| `get_analytics` | `days?` (7/30/90) | totals (clicks, sales, revenue), by_platform, top_countries, by_device |
| `get_top_posts` | — | up to 10 posts ranked by revenue: clicks, sales, revenueCents |

## Behavior notes
- **Money is real, not estimated.** Revenue comes from the user's connected Stripe, matched to the
  post that drove each sale. Report the actual numbers; never invent figures.
- If `get_analytics` / `get_top_posts` return zeros, the account likely hasn't **connected Stripe**
  or **published a tracked post** yet — tell the user to do that at https://seenpaid.com.
- After answering "what made money," it's natural to offer: "want me to schedule more like the top
  earner?" then use `schedule_post`.
- `schedule_for` must be a future ISO-8601 time; omit it to publish immediately.

## Setup (once)
Connect the seenpaid MCP server at `https://go.seenpaid.com/mcp` using a Bearer API key (`sp_…`)
created on the seenpaid dashboard → **AI Agents**. Free trial at https://seenpaid.com.
