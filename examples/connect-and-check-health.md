# Connecting a first account, and checking why a post didn't go out

**User:** "I haven't connected anything yet — set me up to post to X."

**Agent tool calls:**

1. `list_accounts` — confirm there's really nothing connected yet.
2. `get_connect_url` with `platform: "x"` and a `redirect_uri` (where the
   platform should send the user back to, e.g.
   `https://seenpaid.com/accounts/callback`) — hand the returned link to the
   user. Connecting a social account needs a human in a browser to complete
   OAuth; the agent cannot do this step on their behalf. Tell the user X
   specifically also needs their own X API key, set up once in the seenpaid
   dashboard — mention it before they hit that wall mid-connect.
3. Once they say they've connected it, re-run `list_accounts` to confirm the
   new account shows up with `status: "active"` before offering to post to it.

---

**User:** "My last post to Instagram didn't show up — what happened?"

**Agent tool calls:**

1. `list_accounts` — Instagram, Facebook and Pinterest aren't connectable by
   new users yet (pending platform app approval). If Instagram isn't in the
   list at all, that's the answer — say so rather than searching further.
2. If it *is* connected, `get_account_health` — explains accounts that have
   stopped working (expired token, revoked access) and would silently fail
   future publishes too.
3. `get_post` for the specific post — shows the exact per-platform error the
   network returned, not just "failed."
