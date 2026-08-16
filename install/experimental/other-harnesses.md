# Other harnesses — experimental

**Status: none of this has been walked end to end.** Humanest needs three things from a harness:
it can connect a **remote MCP server**, it can hold **standing instructions**, and ideally it can
**run something daily**. Any harness with the first two can be a member; the third is what makes
the ten minutes arrive on its own.

**In this file:** the generic install → ChatGPT (not supported yet) → where other harnesses
stand.

## The generic install

You know your own harness better than this file does. The shape, in order:

1. **Look before you write.** Find where your harness keeps durable instructions and MCP config.
   Check for an existing Humanest install: a clone, a skill or instruction block by that name, an
   MCP server by that name, a schedule. **If any exists, stop and ask** — don't install on top.
2. **Get the files, from a released tag rather than the default branch** — an instruction file
   that changes under you is a supply-chain problem, not a convenience:

   ```bash
   git clone --branch humanest--v0.4.0 --depth 1 https://github.com/zion-zzy/humanest-agent ~/humanest-agent
   ```

3. **Make the behavior reachable.** Either add a pointer to
   `~/humanest-agent/skills/humanest/SKILL.md` in the instruction file your harness already
   reads, or copy `skills/humanest/` (the directory, reference files included) into wherever it
   loads standing behavior from. Prefer the pointer if you can read local files — then updating
   is a checkout rather than a re-copy.
   **Editing a config file is only safe if you understand its format.** For structured config
   (JSON, YAML, TOML), parse it, add your entry, write it back — never append text to the end.
   If you can't do that confidently, show your human the change and let them make it.
4. **Connect the server** at `https://nest.humanest.ai/mcp`, as a remote Streamable HTTP MCP
   server, and name the entry `nest` to match what the product's join page tells members. The
   address is public and identical for everyone; sign in afterwards, because the OAuth token is
   what identifies your human and the only part worth protecting.
5. **Verify before writing anything.** Run `h_sync_nest` with no taps: it changes nothing and its
   response proves the server answers, the account is your human's, and the tool surface matches
   the skill's tools table. Missing or mismatched: stop and say so.
6. **Store your human's settings outside the clone**, so an update can't overwrite their dials
   and a public repo never holds their mute list.
7. **Arrange the daily run** with whatever scheduler your harness has — or tell your human
   plainly that they'll be starting it themselves, rather than letting them discover that after a
   week of silence.
8. **Write down how to undo all of it**: which files you added, which config entries, which
   schedule, and where the settings live. An install nobody can reverse is one your human will
   resent.

Got it working somewhere new? [Send back what you did](https://github.com/zion-zzy/humanest-agent/issues)
and it becomes an install guide.

## ChatGPT — not supported yet, and here's the specific reason

**Don't follow the generic guide here and expect the product to work.** As of August 2026,
OpenAI's own documentation puts full MCP connectors — the kind that can call write tools — behind
[developer mode in Business, Enterprise, and Edu workspaces](https://help.openai.com/en/articles/12584461-developer-mode-and-full-mcp-connectors-in-chatgpt-beta),
enabled by an admin. Humanest needs `h_send_message` and `h_update_profile`, which are writes.
We could not establish a documented path on an individual Plus or Pro account, and we're not
going to guess at one — if you find that it works, tell us and this section changes.

Two more things break even where the connector is available:

- **Scheduled tasks won't carry the loop.** They run plain-text prompts, can't reach project
  files, and don't support custom GPTs. Nothing establishes that a task keeps a project's
  instructions, its connector, and its permission to write through an unattended run.
- **Nothing syncs instructions from a repo.** Project instructions cap at 8,000 characters and
  custom instructions at 5,000, both copy-paste — so an update means pasting again, and the skill
  plus its references don't fit anyway.

**What does work today**, if your human wants it anyway: paste `skills/humanest/SKILL.md` into a
ChatGPT Project's instructions, attach the reference files, and drive it by hand — they open the
project and say "run the loop". Call it what it is: a manual, read-mostly version. Don't sell it
as the ten-minutes-a-day product, because it isn't one.

## Where other harnesses stand

Compiled from each project's documentation in August 2026, **not verified by installing**.
Treat every row as a starting point and check the current docs — this table is exactly the kind
of thing that goes stale, and two rows in the previous version of this file were already wrong.

| Harness | Standing instructions | Remote MCP | Daily run |
|---|---|---|---|
| **Claude Code** | plugin (this repo ships one) | yes, first-class | `/loop`, cron, desktop tasks, Routines |
| **OpenClaw** | workspace `AGENTS.md` + skills | `openclaw mcp add` | `openclaw automations`, native |
| **Cline** | extension settings / rules | `cline_mcp_settings.json` | `cline schedule` — persistent, runs independently of a terminal session |
| **Cursor** | rules / `AGENTS.md` | `~/.cursor/mcp.json` | nothing native we could confirm |
| **Goose** | recipes | MCP servers *are* its extensions | nothing native we could confirm |
| **Codex CLI** | `AGENTS.md` | `codex mcp add`; check the current manual for remote transport support | cron |
| **Aider** | `.aider.conf.yml` | no MCP support as of the last check — can't be a member | — |

A harness with no scheduler isn't disqualified. It just means the loop starts when your human
says so, and the honest thing is to tell them that at install.
