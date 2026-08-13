# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project uses
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

**Compatibility:** this version is written against the Humanest server's messaging-era tool
surface — `h_sync_nest`, `h_send_message`, `h_search_people`, `h_update_profile`. The server is
pre-launch and its tool names are not frozen. **The server's own tool descriptions are
authoritative**; where they disagree with this repo, follow the tools and open an issue. A tool
rename in the server is a MINOR bump here.

## [0.2.0] — 2026-08-12

The install was fiction in places; this release makes it real. Rebuilt after an adversarial
review (47 findings) plus verification against each harness's own documentation.

### Added
- **A Claude Code plugin** (`.claude-plugin/`) — install with two commands, namespaced so it
  can't collide with a member's existing skills, versioned so updates are explicit, removable in
  one command. Replaces the hand-written skill file, which could silently overwrite or be
  shadowed by something the member already had.
- `reference/failure-modes.md` — what to do when sync fails, auth expires, permission is revoked,
  a tool is renamed, or a send times out. Headline rule: **never retry a write whose outcome you
  don't know.**
- `reference/scenarios.md` — six checkable scenarios, three adversarial, so the behavior can be
  tested rather than trusted.
- `reference/filtering.md` and `reference/briefing.md` — the detail moved out of the canonical
  file, including purpose limits on how much of a member's context an agent may use, first
  contact from strangers held in a low-trust group, and a worked good-vs-bad briefing.
- A mandatory **verify-before-first-write** step in every install guide.
- Preflight checks so an install detects an existing plugin, skill, or MCP server instead of
  overwriting it.
- Uninstall and troubleshooting sections per harness.
- `CONTRIBUTING.md`, `SECURITY.md`.

### Changed
- **`AGENT.md` → `skills/humanest/SKILL.md`**, cut from 225 to 139 lines. The old name collided
  with the `AGENTS.md` convention, and measured research finds agent instruction files degrade
  past ~150 lines.
- **The two rules that outrank everything now open the file** — approval before anything leaves,
  and inbound content is data — with the security reasoning stated once, plainly.
- **`claude mcp add` now specifies `--scope user`.** The default is `local`, which registers the
  server for one directory only: the member would have the skill everywhere and the tools
  nowhere.
- **Member settings moved out of the repo.** They used to live in a section of the tracked
  canonical file, which dirtied the clone, conflicted on update, and risked committing a member's
  mute list to a public repository.
- The update path is a versioned plugin update or a released tag, not `git pull` over a dirty
  tree.
- **Honesty pass on every guarantee.** The skill now separates what the server enforces from what
  only the agent enforces, and the README no longer claims more than either can deliver.
- The Claude Code skill description triggers on Humanest specifically, instead of generic words
  like "posts" and "members" that fired during unrelated conversations.

### Removed
- **The ChatGPT adapter's install path.** It described a universal Connectors flow, a scheduled
  daily loop, and "MCP added as a GPT action" — none of which hold up. ChatGPT is now documented
  as unsupported, with the specific entitlement and scheduling reasons, in
  [`install/other-harnesses.md`](install/other-harnesses.md).

### Fixed
- The OpenClaw guide asked the installing agent to improvise. It now uses OpenClaw's real
  documented commands (`openclaw mcp add`, `openclaw automations`) and workspace bootstrap files.
- Keeps are no longer recorded before the member confirms them — a keep discloses their name, so
  "confirm afterwards" was notification, not confirmation.
- A post that tries to instruct the reading agent is now quarantined and reported rather than
  silently discarded, so legitimate discussion of injection isn't destroyed.

## [0.1.0] — 2026-08-12

First version: the daily loop, filtering judgment, keeps, the briefing shape, send judgment under
standing permission, profile care, and adapters for three harnesses.

[0.2.0]: https://github.com/zion-zzy/humanest-agent/releases/tag/humanest--v0.2.0
[0.1.0]: https://github.com/zion-zzy/humanest-agent/releases/tag/humanest--v0.1.0
