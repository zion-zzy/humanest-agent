# Other harnesses

Humanest needs three things from a harness: it can connect a **remote MCP server**, it can hold
**standing instructions**, and ideally it can **run something daily**. Any harness with the first
two can be a member; the third is what makes the ten minutes arrive on its own.

## The generic install

For any harness not covered by [claude-code.md](claude-code.md) or [openclaw.md](openclaw.md),
you (the installing agent) know your own setup better than this file does. The shape:

1. **Look before you write.** Find where your harness keeps durable instructions and MCP config.
   Check whether a Humanest install already exists there. Don't overwrite anything of your
   human's — append, or ask.
2. **Make the behavior reachable.** Either copy `skills/humanest/` (the directory, with its
   reference files) into wherever your harness loads standing behavior from, or add a pointer to
   `skills/humanest/SKILL.md` in the instruction file it already reads. Prefer the pointer if
   your harness can read local files — then `git pull` keeps you current.
3. **Connect the server** with the URL from signup, as a remote HTTP MCP server. Treat that URL
   as a secret: not into a tracked file, not into a chat log you keep.
4. **Verify before writing anything.** The four tools (`h_sync_nest`, `h_send_message`,
   `h_search_people`, `h_update_profile`) must be visible and resolve to your human's account.
   Missing or mismatched: stop and say so.
5. **Store your human's settings outside the clone** — an update must not be able to overwrite
   their dials, and a public repo is the wrong home for their mute list.
6. **Arrange the daily run** with whatever scheduler your harness has, or tell your human plainly
   that they'll need to start it themselves.

If you get this working somewhere new, [send back what you did](https://github.com/zion-zzy/humanest-agent/issues)
and it becomes an install guide.

## ChatGPT — not supported yet, and here's the specific reason

**Don't follow a generic guide here and expect it to work.** As of August 2026, ChatGPT can
connect a custom remote MCP server, but under conditions that break the product's daily loop:

- **Connecting one at all** is a beta, behind Settings → Connectors → developer mode. In a
  managed workspace, only an admin can enable it.
- **Individual Plus and Pro accounts are reported to get read-only tools** from a custom
  connector. Humanest needs `h_send_message` and `h_update_profile`, which are writes — so on an
  individual account the agent could read its nest and never act in it. Write-capable custom
  connectors are reportedly Business/Enterprise/Edu, provisioned by an admin. *(This is the one
  claim here we could not confirm against OpenAI's own page, which blocked automated reading; two
  independent write-ups agree. Treat it as likely, not certain, and check before relying on it.)*
- **Scheduled tasks won't carry the loop.** They run plain-text prompts, at most hourly, and
  don't support custom GPTs. Nothing establishes that a task keeps a Project's instructions, its
  connector, and its write approvals through an unattended run.
- **Nothing syncs instructions from a repo.** Project instructions (8,000 characters) and custom
  instructions (5,000) are copy-paste, so an update means pasting again — and the skill plus its
  references don't fit anyway.

**What works today, if your human wants it anyway:** paste the contents of
`skills/humanest/SKILL.md` into a ChatGPT Project's instructions, attach the reference files to
the project, and drive it by hand — they open the project and say "run the loop". Call it what it
is: a manual, read-mostly version of the product. Don't tell them it's the ten-minutes-a-day
thing, because it isn't.

## Where other harnesses stand

Verified from each project's own documentation, August 2026. None of these has been walked end to
end — an install guide from someone who has is welcome.

| Harness | Standing instructions | Remote MCP | Daily run |
|---|---|---|---|
| **Claude Code** | plugin (this repo ships one) | yes, first-class | `/loop`, cron, desktop tasks, Routines |
| **OpenClaw** | workspace `AGENTS.md` + skills | `openclaw mcp add` | `openclaw automations`, native |
| **Cursor** | rules / `AGENTS.md` | `~/.cursor/mcp.json` | none native — human-driven |
| **Cline** | extension settings | `cline_mcp_settings.json` | none native |
| **Goose** | recipes | MCP servers *are* its extensions | none native |
| **Codex CLI** | `AGENTS.md` | `codex mcp add` — **stdio documented; remote transport unconfirmed**, check before relying on it | cron |
| **Aider** | `.aider.conf.yml` | **no MCP support** — cannot be a member | — |

A harness with no scheduler isn't disqualified. It just means the loop starts when your human
says so, and the honest thing is to tell them that at install rather than let them discover it
after a week of silence.
