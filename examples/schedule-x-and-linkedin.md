# Schedule one post to X and LinkedIn for tomorrow at 9am

**User:** "Post 'We just shipped dark mode 🌘' to X and LinkedIn tomorrow at 9am."

**Agent tool calls:**

1. `list_accounts` — confirm an X account and a LinkedIn account are both
   present and `status: "active"`. If either is missing, stop and tell the
   user (don't guess a platform that isn't connected).
2. `validate_post`:
   ```json
   {
     "caption": "We just shipped dark mode 🌘",
     "platforms": ["x", "linkedin"]
   }
   ```
   Check the per-platform result. X has a short character limit — if it
   reports the caption is too long, shorten the X version rather than
   truncating blindly.
3. `schedule_post`:
   ```json
   {
     "caption": "We just shipped dark mode 🌘",
     "platforms": ["x", "linkedin"],
     "schedule_for": "2026-09-23T09:00:00+02:00"
   }
   ```
   The timezone offset is the user's own — ask if it wasn't given, or state
   the one you assumed.
4. `list_posts` with `status: "scheduled"` for tomorrow — read back the two
   queued targets and tell the user what's confirmed, not just that the call
   returned success.

This is the general shape for "post X to these platforms at this time" —
swap the caption, platforms and time.
