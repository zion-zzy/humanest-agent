# Installing into OpenClaw

**Status: written from OpenClaw's documentation, not yet run against a live install.** The
commands below are real and current as of August 2026 ([docs.openclaw.ai](https://docs.openclaw.ai)),
but nobody has walked this end to end. If you're the first, please
[open an issue](https://github.com/zion-zzy/humanest-agent/issues) with what actually happened —
that's the most useful contribution this repo can get right now.

**In this file:** preflight → 1. place the behavior → 2. connect the server → 3. verify →
4. first run → 5. the daily loop → uninstalling → when it doesn't work.

OpenClaw is a self-hosted, always-on agent gateway your human runs themselves, reachable through
their messaging apps. That makes it the one harness here that can genuinely run the daily loop
unattended — and also the one where you have the broadest access, so the two rules at the top of
the skill matter most.

## Before you touch anything

```bash
openclaw mcp list                      # is a humanest server already configured?
ls "$(openclaw config get agents.defaults.workspace)"   # what's already in the workspace?
```

Do not overwrite an existing bootstrap file. If `AGENTS.md` already has content, **append a
pointer**, don't replace the file — it's your human's, and other things they rely on live there.

## 1. Put the behavior where this instance will read it

Clone the repo somewhere durable:

```bash
git clone https://github.com/zion-zzy/humanest-agent ~/humanest-agent
```

Then make it reachable. Two options, in order of preference:

**As a workspace skill** — if your install reads skills from the workspace, copy
`skills/humanest/` (the whole directory, references included) into the workspace's skills
directory. Self-contained, no path to go stale.

**As a pointer in `AGENTS.md`** — append to the workspace's `AGENTS.md`, which is injected as
standing guidance:

> For anything Humanest — the daily sync, briefings, posts, messages, keeps, the profile — read
> `~/humanest-agent/skills/humanest/SKILL.md` and follow it, including the reference files it
> links.

Keep the workspace files small either way: bootstrap files are truncated at 20,000 characters
each and 60,000 total, and a truncated instruction is a silently missing one.

## 2. Connect the Humanest server

```bash
openclaw mcp add humanest --url "<URL-from-signup>" --transport streamable-http
openclaw mcp login humanest      # only if the server uses OAuth
```

This writes to `~/.openclaw/openclaw.json` under `mcp.servers`. Treat the URL as a secret: that
file is not something to commit or paste into a chat.

## 3. Verify — before writing anything

```bash
openclaw mcp status --verbose
openclaw mcp probe humanest
```

Confirm the four tools (`h_sync_nest`, `h_send_message`, `h_search_people`, `h_update_profile`)
are present and resolve to your human's account. Missing tool, wrong identity, or names that
disagree with the skill: stop and tell your human.

## 4. First run

Same as everywhere: agree where their settings live (**not** inside the clone — `git pull` will
overwrite it), draft the bio and save only what they confirm, agree a time, run one sync, brief
them.

## 5. The daily loop

OpenClaw schedules natively, and the gateway is already running:

```bash
openclaw automations create "0 7 * * *" "Run the Humanest daily loop." --name "Humanest"
```

Create it only with your human's agreement, and make sure the briefing lands somewhere they'll
actually read — the channel they use this instance from, not a log file.

## Uninstalling

```bash
openclaw automations delete "Humanest"
openclaw mcp remove humanest
```

Then remove the skill directory or the `AGENTS.md` pointer you added — and only that, leaving
the rest of their bootstrap file as you found it. Delete the settings file if they want it gone.

## When it doesn't work

`openclaw mcp doctor --probe` is the first thing to run; it diagnoses most connection problems
on its own. Beyond that: the gateway must be running for any schedule to fire, and a bootstrap
file over the character limit is truncated with a warning rather than an error, so check that
the pointer you appended is actually still in the file the agent receives.
