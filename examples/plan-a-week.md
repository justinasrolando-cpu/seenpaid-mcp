# Plan a week of posts with bulk_schedule

**User:** "Plan out 5 posts about our new feature for this week, spread across
X and LinkedIn."

**Agent approach:**

1. `list_accounts` — confirm which platforms are actually connected.
2. `get_next_slot` (optional) — if the user has no time preference, ask it
   for open times that have historically earned this account the most, one
   per day, instead of guessing a schedule.
3. Draft the 5 captions and **show them to the user before sending
   anything** — a week of posts is exactly the kind of batch that deserves a
   one-line confirmation first.
4. Once approved, one `bulk_schedule` call:
   ```json
   {
     "posts": [
       { "caption": "Monday's caption…", "platforms": ["x"], "schedule_for": "2026-09-23T09:00:00+02:00" },
       { "caption": "Tuesday's caption…", "platforms": ["linkedin"], "schedule_for": "2026-09-24T09:00:00+02:00" },
       { "caption": "Wednesday's caption…", "platforms": ["x", "linkedin"], "schedule_for": "2026-09-25T09:00:00+02:00" },
       { "caption": "Thursday's caption…", "platforms": ["x"], "schedule_for": "2026-09-26T09:00:00+02:00" },
       { "caption": "Friday's caption…", "platforms": ["linkedin"], "schedule_for": "2026-09-27T09:00:00+02:00" }
     ]
   }
   ```
   Each item in `posts` is scheduled independently — if one fails (say, a
   caption too long for X), the rest still go through. Report any failures
   by name rather than a generic "some posts failed."
5. `list_posts` for the week's date range — confirm all five landed and read
   the actual queued times back to the user.
