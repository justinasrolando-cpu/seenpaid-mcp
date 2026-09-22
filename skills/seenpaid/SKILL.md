---
name: seenpaid
description: Schedule and publish social media posts to X, LinkedIn, Bluesky, Threads, TikTok, Mastodon, Discord and 14 more platforms through seenpaid's hosted MCP server, check a caption before it goes out, see what is queued, and read which posts actually made money from the user's own Stripe. Use when the user wants to post, cross-post, schedule, plan a content calendar, check what is scheduled, or ask which social posts drove sales.
license: MIT
compatibility: Needs an MCP-capable client (Claude Code, Claude, ChatGPT, Cursor, Codex, Gemini CLI, etc.), network access to https://api.seenpaid.com/mcp, and a seenpaid account with at least one social account connected.
metadata:
  author: seenpaid
  homepage: https://seenpaid.com/agent
  version: "1.0"
---

# seenpaid — schedule social posts from the agent

seenpaid is a hosted social media scheduler. Its MCP server gives you 50 tools
to post, schedule, check and measure. This file tells you how to use them
without surprising the user. The full tool list is in
[references/tools.md](references/tools.md).

## Connect

The server is `https://api.seenpaid.com/mcp` (streamable HTTP).

- **Claude / ChatGPT / Cursor (UI):** add it as a custom connector and sign in
  when asked (OAuth). No key to paste.
- **Config-file clients (Claude Code, Codex, Gemini CLI…):** create a key in
  seenpaid → Settings → API (it starts with `sp_`) and send it as
  `Authorization: Bearer sp_…`. For Claude Code:

  ```bash
  claude mcp add --transport http seenpaid https://api.seenpaid.com/mcp \
    --header "Authorization: Bearer $SEENPAID_API_KEY"
  ```

Connecting a new social network needs a human in a browser. If the user has
nothing connected, call `get_connect_url` and hand them the link — you cannot
do the OAuth for them.

## Rules that matter

1. **Start with `list_accounts`.** You can only post to accounts that are
   already connected and `active`. Never promise a platform you have not seen
   in that list.
2. **Always pass `platforms` (or `account_ids`) explicitly.** If you leave
   both out, `schedule_post` posts to *every* connected account.
3. **Never leave out `schedule_for` unless the user said "now".** Without it
   the post publishes immediately. `schedule_for` is ISO-8601 with an
   explicit offset (`2026-09-23T09:00:00+02:00`). If the user gives a time
   with no timezone, ask, or state the one you are assuming.
4. **Call `validate_post` before `schedule_post`** whenever one caption goes
   to more than one platform. It reports, per platform, whether it would
   publish and exactly why not (too long by N characters, media required,
   account disconnected). Fix those first.
   - When it includes a `signals` object, those are advisory probabilities
     from Jev about how the caption reads (spam-like, weak opening, needs
     context, promotional). Mention the `notes` to the user; they never block
     a post and you should not treat them as a verdict.
5. **Confirm before anything irreversible or public.** Publishing now,
   `bulk_schedule` of many posts, and `delete_post` all deserve a one-line
   confirmation with what will happen. Prefer `cancel_post` to `delete_post`.
6. **Read back your own work.** After scheduling, call `list_posts` (with
   `from`/`to` or `status: "scheduled"`) and tell the user what is queued,
   rather than asserting success.

## Platform limits you must be honest about

These come from the networks, not from you — say so plainly if they apply:

- **Instagram, Facebook, Pinterest:** not connectable by new users yet
  (waiting on the platforms' app approval). If they are not in
  `list_accounts`, you cannot post there.
- **X:** needs the user's own X API key, set up in the seenpaid dashboard.
- **TikTok:** videos go to the creator's TikTok **drafts**; they finish and
  publish the post in the TikTok app. Tell the user that.
- Some platforms reject text-only posts. `get_platform_requirements` returns
  caption limits and media rules per platform; `validate_post` applies them.

## Common jobs

**Post one thing to several platforms, tomorrow at 9:**

1. `list_accounts` → confirm the platforms are connected.
2. `validate_post` with the caption and those platforms. If X is over its
   limit, write a shorter X version.
3. `schedule_post` with `platforms`, `schedule_for`, and
   `per_account_captions` for the shortened versions (keyed by account id).
4. `list_posts` for tomorrow → report what is queued.

**Plan a week:** draft the posts, show them to the user, then one
`bulk_schedule` call. Each item is independent; report any that failed.
`get_next_slot` suggests open times if the user has no preference.

**With an image:** `generate_image` (text prompt) or `add_media_from_url`
returns a media id; pass it in `media_ids`. `list_media` reuses past uploads.

**"What went out / what failed?":** `list_posts` with `status: "published"`
or `"failed"`; `get_account_health` explains accounts that stopped working.

**"Which posts made money?":** only works if the user connected Stripe for
attribution. `get_top_posts`, `get_post_revenue`, `get_channel_roi`.
Check `get_attribution_health` before drawing conclusions from low numbers —
a low match rate means missing data, not a bad post.

## Don't

- Don't post to platforms the user didn't name.
- Don't invent engagement or revenue numbers; read them or say you can't.
- Don't retry a failed `schedule_post` blindly — `list_posts` first, or you
  may double-post.
