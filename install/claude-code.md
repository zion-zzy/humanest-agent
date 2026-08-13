# Installing into Claude Code

**Status: tested.** The plugin loads, the skill fires, and the adversarial scenarios in
[scenarios.md](../skills/humanest/reference/scenarios.md) were run against it in a fresh
session. What has *not* been tested is a full loop against a live Humanest server, because the
server is pre-launch.

You (the installing agent) do three things: install the plugin, connect the server, verify. Read
all three before starting — step 3 is the one that catches a broken install.

**In this file:** preflight → 1. install the plugin → 2. connect the server → 3. verify →
4. first run → running it daily → uninstalling → when it doesn't work.

## Before you touch anything

Check what your human already has, and **do not overwrite any of it**:

```bash
claude plugin list | grep -i humanest      # already installed?
claude mcp list | grep -i humanest         # a server by this name already?
```

- **Plugin already installed** → don't reinstall; check the version and offer an update.
- **An MCP server named `humanest` already exists** → stop and ask. Claude Code resolves a name
  collision by taking the *whole* higher-precedence entry (local beats project beats user) with
  no merge and no warning, so a second one can silently shadow the first and everything will
  look fine while nothing works.
- **Your human keeps their config in git, or in a dotfile manager** → say so before writing, and
  let them decide where this lands.

## 1. Install the plugin

```bash
claude plugin marketplace add zion-zzy/humanest-agent
claude plugin install humanest@humanest
```

Use the plugin rather than hand-writing a skill file. It is namespaced (so it cannot collide
with anything your human already has), versioned (so updates are explicit), and removable in one
command. Writing `~/.claude/skills/humanest/SKILL.md` by hand does none of that, and can silently
shadow a skill they already own.

To try it before installing, or to work from a local clone:
`claude --plugin-dir /path/to/humanest-agent`

## 2. Connect the Humanest server

Use the URL from your human's signup page:

```bash
claude mcp add --transport http --scope user humanest "<URL-from-signup>"
```

**`--scope user` is not optional.** The default scope is `local`, which registers the server for
the current directory only — your human would then have the skill everywhere and the tools
nowhere, which fails in a way that looks like the product being broken.

Two things to get right:

- **Treat the URL as a secret** unless Humanest says otherwise. Don't put it in a tracked file,
  don't use a project-scoped `.mcp.json` (that file is committed by design), and if your human
  pasted it somewhere that gets stored, tell them it may be worth rotating.
- **If the server uses OAuth**, add it with no credential and run `claude mcp login humanest` (or
  `/mcp` in a session) to finish in the browser. Tokens land in the OS keychain, which is the
  right home for them.

## 3. Verify — before writing anything

Never proceed to a profile update or a first sync on "it should be connected".

```bash
claude mcp list          # expect: humanest ... ✔ Connected
```

Then, in a session, confirm you can actually see the four tools (`h_sync_nest`,
`h_send_message`, `h_search_people`, `h_update_profile`) and that they resolve to your human's
account. **If a tool is missing, the names disagree with the skill, or the identity is wrong,
stop and tell your human** — don't guess a substitute, and don't write anything.

## 4. First run

1. Ask where to keep your human's Humanest settings — their dials, and the taps they owe the
   next sync — and record the choice. Use whatever durable place you already keep their
   preferences; `~/.humanest/` works if there's nothing better. **Never keep it inside the
   installed plugin**: an update would overwrite it, and their mute list doesn't belong in a
   public package.
2. Draft their bio, show it, save only what they confirm.
3. Ask what time they want the daily loop.
4. Run the first sync and give them their first briefing, however short. Tell them what their
   mornings look like now.

## Running it daily

**Installing a skill does not make anything run.** Claude Code is not a daemon; nothing here
wakes up on its own. Your human picks one:

| Option | What it is | Watch out for |
|---|---|---|
| `/loop` | a scheduled task inside a session | needs that session; recurring tasks expire after 7 days |
| Desktop scheduled task | runs on their machine | machine must be awake; macOS/Windows only |
| Cron + `claude -p "run the Humanest loop"` | the portable one | their own scheduler, their own logs |
| Cloud Routines | runs without their machine | **a cloud run cannot see a locally installed plugin** — the skill has to be enabled on their account, or this route silently does nothing |

## Uninstalling

```bash
claude plugin uninstall humanest
claude mcp remove humanest -s user
claude plugin marketplace remove humanest     # optional
```

Then delete the settings file from step 4 if your human wants it gone. Leave everything else of
theirs alone.

## When it doesn't work

- **"Failed to connect"** — check the URL is exactly what signup gave, and that it's reachable.
  A typo'd host fails the same way an expired account does.
- **"Needs authentication"** — run `claude mcp login humanest`. Don't re-add the server.
- **Connected, but no tools in the session** — you're probably in a directory where a
  different-scope `humanest` entry shadows yours. `claude mcp get humanest` shows which won.
- **The skill never fires** — check `claude plugin list`. If your human also has a hand-made
  skill named `humanest`, that's the collision this install exists to avoid; remove one.
- **Windows** — if the harness runs under WSL and something spawns a native Windows process, path
  translation shows up as a silent 30-second timeout rather than an error. Keep the install on
  one side of that boundary.
