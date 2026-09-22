# "Which posts made money last month?"

**User:** "Which of my posts made money last month?"

**Agent tool calls:**

1. `get_attribution_health` — check this **first**. If the Stripe match rate
   is low, that means tracking data is missing (Stripe not connected, or
   posts published without a tracked link) — not that nothing sold. Say so
   plainly rather than reporting zeros as "no posts earned."
2. `get_top_posts` with a 30-day window — ranked by revenue. This answers the
   question directly: which post earned the most.
3. Optionally `get_channel_roi` — if the user asks a follow-up like "which
   platform is worth my time," this ranks platforms by revenue, revenue per
   click, and revenue per post instead of follower counts.
4. For a single post the user names, `get_post_revenue` gives clicks, sales
   and revenue for just that one.

**Related jobs the same tools cover:**

- "What's not working?" → `get_dead_posts` — posts that got clicks but never
  converted, the most actionable "stop making this" list.
- "What just happened?" → `get_money_feed` — the live stream of clicks,
  sales and refunds.
- "How are we doing overall?" → `summarize_performance` — one briefing
  instead of five separate calls.

Never invent a revenue number. If `get_attribution_health` shows nothing is
tracked yet, tell the user that instead of reporting €0 as a verdict on their
content.
