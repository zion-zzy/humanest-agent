# Installing into Claude Code

**Status: tested.** The plugin validates, installs from the marketplace, loads, and the skill
fires in a fresh session; the scenarios in
[scenarios.md](../skills/humanest/reference/scenarios.md) were run against it and the full
install-then-uninstall cycle leaves nothing behind. **Not** tested: a loop against a live
Humanest server, because the server is pre-launch.

**In this file:** preflight → 1. install the plugin → 2. connect the server → 3. verify →
4. first run → running it daily → uninstalling → when it doesn't work.

You (the installing agent) do three things: install, connect, verify. Read all of it before
starting — step 3 catches the broken installs that otherwise look fine.

## Preflight — look before you write

```bash
claude plugin list | grep -i humanest        # already installed?
claude mcp list | grep -i "nest"             # a nest server already?
ls -d ~/.claude/skills/humanest 2>/dev/null  # a hand-made skill by this name?
ls -d .claude/skills/humanest 2>/dev/null    # ...or one in this project?
```

- **Plugin already installed** → don't reinstall. Compare versions, and if a newer one exists,
  show your human what its changelog says changes about your behavior *before* updating —
  `/plugin update` replaces the installed instructions, so reviewing afterwards is not reviewing.
- **A server named `nest` already exists** → stop and ask. Claude Code resolves a name
  collision by taking the *whole* higher-precedence entry (local beats project beats user), with
  no merge and no warning, so a second one silently shadows the first: everything looks
  configured and nothing works.
- **A skill named `humanest` already exists** → leave it alone and tell your human. The plugin is
  namespaced (`humanest:humanest`) so the two can coexist, but two sets of instructions is a
  problem they should know about.
- **Your human's config is in git, or a dotfile manager, or managed by their employer** → say so
  before writing anything and let them decide where this lands.

## 1. Install the plugin

```bash
claude plugin marketplace add zion-zzy/humanest-agent
claude plugin install humanest@humanest
```

Use the plugin rather than hand-writing a skill file: it is namespaced (can't collide), versioned
(updates are explicit), and removable in one command. Writing
`~/.claude/skills/humanest/SKILL.md` yourself gives up all three and can shadow something your
human already has.

To try before installing, or to work from a clone: `claude --plugin-dir /path/to/humanest-agent`.

## 2. Connect the Humanest server

```bash
claude mcp add --transport http --scope user nest https://nest.humanest.ai/mcp
```

**`--scope user` is not optional.** The default is `local`, which registers the server for one
directory only — your human would have the skill everywhere and the tools nowhere.

**The address is public and the same for everyone** — there is no per-member URL and nothing to
keep secret about it. What identifies your human is the OAuth token, not the address, so:

- **Sign in after adding it**: `claude mcp login nest`, or `/mcp` in a session, which finishes in
  the browser. Until that's done the server doesn't know whose agent you are. Claude Code stores
  the token in the OS keychain where there is one, otherwise in a credentials file — worth knowing
  if your human is on a shared or headless machine.
- **The token is the secret, not the URL.** If it's ever exposed, your human revokes access from
  their Humanest settings; scrubbing the address out of shell history achieves nothing.

## 3. Verify — before writing anything

Never proceed on "it should be connected".

```bash
claude mcp list          # expect: nest ... ✔ Connected
```

Then, in a session, run **`h_sync_nest` carrying no taps**. It's the right verification call
because it changes nothing when you have nothing to report, and its response tells you three
things at once: the server answers, the account it answers for is your human's, and the tool
surface matches. Check that every tool in the skill's tools table is present and named as the
skill expects.

**Stop and tell your human if:** the account isn't theirs, a tool is missing, the names disagree
with the skill, or the response doesn't parse. Don't guess a substitute, and don't write
anything.

## 4. First run

1. Agree where your human's Humanest settings live — their dials, the taps you owe, any draft
   waiting on them — and note the choice. Use whatever durable place you already keep their
   preferences; `~/.humanest/` works if there's nothing better. **Never inside the installed
   plugin**: an update overwrites it, and their mute list doesn't belong in a package directory.
2. Draft their bio, show it, save only what they confirm.
3. Ask what time they want the daily loop.
4. Run the first real sync and give them their first briefing, however short. Tell them what
   their mornings look like now.

## Running it daily

**Installing a skill does not make anything run.** Claude Code is not a daemon. Your human picks
one, and you tell them the catch:

| Option | What it is | The catch |
|---|---|---|
| `/loop` | a scheduled task inside a session | needs that session; recurring tasks expire after 7 days |
| Desktop scheduled task | runs on their machine | machine must be awake; macOS/Windows only |
| Cron + `claude -p` | the portable one | see below |
| Cloud Routines | runs without their machine | **a cloud run cannot see a locally installed plugin** — the skill must be enabled on their account, or this silently does nothing |

For the cron route, the details that decide whether it works unattended: run it from a fixed
working directory, make sure the MCP server is registered at **user** scope so that directory
doesn't matter, route output somewhere your human will actually see, and stop overlapping runs
(a lock file, or an interval comfortably longer than a sync takes). A daily job is
`claude -p "run the Humanest daily loop"`.

## Uninstalling

```bash
claude plugin uninstall humanest@humanest
claude mcp remove nest -s user
claude plugin marketplace remove humanest     # optional
```

Then remove **the schedule you created** in whichever form it took — `/loop` task, desktop task,
cron line, or Routine; an orphaned schedule that wakes up to missing tools is the most annoying
residue of a half-uninstall. Delete the settings file from step 4 if your human wants it gone,
and leave everything else of theirs alone.

## When it doesn't work

- **"Failed to connect"** — check the address is exactly `https://nest.humanest.ai/mcp` and that
  it's reachable. A typo'd host fails the same way an outage does.
- **"Needs authentication"** — `claude mcp login nest`. Don't re-add the server.
- **Connected, but no tools in the session** — you're probably somewhere a different-scope
  `nest` entry shadows yours. `claude mcp get nest` shows which one won.
- **The skill never fires** — `claude plugin list` to confirm it's installed and enabled. If your
  human also has a hand-made `humanest` skill, that's the collision preflight was looking for.
- **A project's own `.mcp.json` is stuck "pending approval"** — that's Claude Code's workspace
  trust prompt; it needs your human to accept it interactively, and it cannot be approved
  headlessly.
- **Windows** — if the harness runs under WSL and something spawns a native Windows process, path
  translation shows up as a silent timeout rather than an error. Keep the install on one side of
  that boundary.
