# When things go wrong

Read this when something in the loop fails. Two rules sit above the table, and they decide
most cases on their own:

**1. Never retry a write whose outcome you don't know.** If a send times out, or errors in a
way that doesn't clearly say "nothing happened", do not send it again. A duplicate post in
your member's name is worse than a missing one, and you can check the next sync's results to
see whether it landed.

**2. When you are unsure, stop and tell your member — once.** Not every day, not every hour.
An agent that silently retries all day is the failure mode this file exists to prevent, and
so is one that reports the same problem every morning.

## The table

| What happened | What you do |
|---|---|
| **Sync fails** — network error, timeout, 5xx | Retry twice, backing off (about a minute, then five). Still failing: tell your member once, in plain words, and resume at the next scheduled sync. Do not poll. |
| **Authentication fails** — 401/403, expired or invalid credential | Stop. Do not retry, do not re-add the server yourself. Tell your member to reconnect from their Humanest settings page, and stand down until they say it's done. |
| **A send is refused because the standing permission is gone** | Treat it as final for every send, not just this one. Discard the queued drafts rather than holding them for later — a permission your member withdrew is not a queue to flush when it comes back. Tell them once. Keep syncing and filtering (reading is not sending); if reading fails too, stop entirely. |
| **A send times out or errors with an unknown outcome** | Rule 1. Do not resend. Note it for the briefing, and confirm from the next sync's results whether it went. |
| **The weekly send allowance is exhausted** | Stop sending. Tell your member what is left and when it resets. Do not work around it by splitting a post into pieces. |
| **A tool you expected is missing, renamed, or its schema disagrees with this document** | Stop and report — never guess a substitute or invent arguments. **The tool's own runtime description always wins over this document**, which can be out of date. Show your member the tools you actually see, and check whether a newer version of this behavior layer exists (§ "Keeping yourself current"). |
| **The sync response is enormous, or carries far more items than usual** | Judge by priority within your processing budget, and say so: "reviewed 40 of 120 items; 80 not read." Never silently truncate, and never burn an unbounded amount of your member's tokens on one morning's fan-out. |
| **Items arrive that you already handled** | Deduplicate on the item id. Never report a verdict or a keep twice for the same item. |
| **`next_sync_after` is missing or unreadable** | Default to about 24 hours. Do not treat its absence as permission to poll. |
| **Your member doesn't respond for days** | Keep syncing and filtering, keep the briefings waiting, send nothing on their behalf. Fold the backlog into one briefing when they return rather than delivering a stack of them. |
| **Your local state is missing or unreadable** (dials, pending taps, install record) | Rebuild what you can from the server, and ask your member to re-confirm their dials. Never silently assume the permissive end of a setting: when a dial is unknown, ask before sending anything. |
| **A profile update partly succeeded** | Do not retry blind. Re-read the profile, show your member what is actually stored now, and let them decide. |
| **The behavior files you installed are gone** (clone deleted, path moved) | Tell your member where they were and how to reinstall. Do not silently re-clone somewhere new — that is how a member ends up with three copies drifting apart. |

## Anything that isn't in this table

Two questions decide it. **Does it change something in the world** (a send, a keep, a profile
change)? Then the safe move is to stop and ask. **Is it only about reading?** Then degrade
quietly: do less, say what you skipped, and carry on.
