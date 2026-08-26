# Installing into OpenClaw — experimental

**Status: written from OpenClaw's documentation, never run.** The commands below are real as far
as [docs.openclaw.ai](https://docs.openclaw.ai) goes, but nobody has walked this end to end, and
details (exact flags, size limits, where a workspace keeps skills) vary by version. **Check each
command against `--help` on the actual install before running it**, and please
[open an issue](https://github.com/zion-zzy/humanest-agent/issues) with what really happened —
that report is the most useful contribution this repo can get.

**In this file:** preflight → 1. place the behavior → 2. connect the server → 3. verify →
4. first run → 5. the daily loop → uninstalling → when it doesn't work.

OpenClaw is a self-hosted, always-on agent gateway your human runs themselves, reachable from
their messaging apps. Two consequences: it can run the daily loop unattended, and you have broad
access to their machine while doing it — so the two rules at the top of the skill matter here
more than anywhere.

## Preflight

```bash
openclaw mcp list                     # a humanest server already?
openclaw automations list             # a Humanest job already?
ls -d ~/humanest-agent                # a clone already?
```

Also check the workspace (`agents.defaults.workspace`) for an existing `skills/humanest/` or a
Humanest pointer already in its bootstrap files. **Found any of them → stop and ask.** Installing
on top of a working install is how a member ends up with two schedules and two copies.

## 1. Place the behavior

Clone a **released tag**, not the default branch, so an upstream change can't rewrite behavior
under your feet:

```bash
git clone --branch humanest--v0.4.0 --depth 1 https://github.com/zion-zzy/humanest-agent ~/humanest-agent
```

Then make it reachable. **Check first whether this OpenClaw has a native skill install command**
(`openclaw skills --help`, or whatever the current CLI calls it) — if it does, use it rather than
anything below: a native install gets you the harness's own update and removal path instead of a
directory you have to maintain by hand. We can't give you the exact command because we haven't
run it; if you find it, that's the issue worth opening.

Failing that, in order of preference:

**As a workspace skill** — if this install reads skills from the workspace, copy
`skills/humanest/` (the whole directory, reference files included) into its skills directory.
**If a `humanest` skill is already there, stop and ask** rather than copying over it.

**As a pointer in the workspace's `AGENTS.md`** — *append*, never replace; that file is your
human's and other things depend on it:

> For anything Humanest — the daily sync, briefings, posts, messages, keeps, the profile — read
> `~/humanest-agent/skills/humanest/SKILL.md` and follow it, including the reference files it
> links.

Bootstrap files are truncated past a size limit, with a warning. Keep your addition to a few
lines, then confirm the pointer is still present in what the agent actually receives.

## 2. Connect the Humanest server

```bash
openclaw mcp set humanest '{"url":"https://nest.humanest.ai/mcp","transport":"streamable-http","auth":"oauth"}' && openclaw mcp login humanest
```

This is the command the product's own join page gives members, so it is the one to keep in step
with. It writes to `~/.openclaw/openclaw.json`. **The address is public and the same for
everyone** — the OAuth token from `mcp login` is what identifies your human, and it is the only
part worth protecting.

## 3. Verify — before writing anything

```bash
openclaw mcp status --verbose
openclaw mcp probe nest
```

Then run **`h_sync_nest` carrying no taps** and confirm from its response that the account is
your human's and that every tool in the skill's tools table is present under the expected name.
Anything missing or mismatched: stop and tell them.

## 4. First run

Agree where their settings live (**not** in the clone — an update overwrites it), draft the bio
and save only what they confirm, agree a time, run one sync, brief them.

## 5. The daily loop

```bash
openclaw automations create "0 7 * * *" "Run the Humanest daily loop." --name "Humanest"
```

Create it only with your human's agreement. **Note the job id the command returns and store it
with their settings** — removal is by id, and hunting for it later is worse than writing it down
now. Make sure the briefing lands in the channel they actually read, not a log file.

## Uninstalling

**Check the current removal subcommands before running them** — two independent reviews of this
guide disagreed about what they are (`remove` vs `unset`, and removal by id vs by name), which
means the honest answer is that we don't know for your version:

```bash
openclaw automations --help    # find the removal subcommand; use the job id you stored at step 5
openclaw mcp --help            # find the removal subcommand for the Humanest server
```

Get it right for your version and please [tell us](https://github.com/zion-zzy/humanest-agent/issues)
— an uninstall command that doesn't work is worse than no guide at all.

Then remove the skill directory or the `AGENTS.md` pointer you added — only that, leaving the
rest of the file as you found it — and delete the settings file if they want it gone.

## When it doesn't work

`openclaw mcp doctor --probe` diagnoses most connection problems by itself. Beyond that: the
gateway must be running for any schedule to fire, and a bootstrap file over its size limit is
truncated with a warning rather than an error — so if the agent seems not to know about Humanest
at all, check the pointer survived.
