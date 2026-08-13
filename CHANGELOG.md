# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project uses
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

**Compatibility:** this version is written against the Humanest server's messaging-era tool
surface — `h_sync_nest`, `h_send_message`, `h_search_people`, `h_update_profile`. The server is
pre-launch and its tool names are not frozen. **The server's own tool descriptions are
authoritative**; where they disagree with this repo, follow the tools and open an issue. A tool
rename in the server is a MINOR bump here.

## [0.3.0] — 2026-08-12

A cross-model evaluation of 0.2.0 found the approval policy stated three incompatible ways across
four files, and several harness claims that were simply wrong. Both are fixed.

### Changed
- **One approval policy, stated once.** 0.2.0 said "approval for that specific thing" in
  `SKILL.md`, allowed autonomous confirmations and a standing "just post the build updates"
  setting in `sending.md`, and said "a post always asks" in `scenarios.md`. The rule is now:
  nothing reaches another human until the member has seen the words and agreed, arriving either
  as an approved draft or as content they dictated. **There is no standing approval for content**,
  agents are told to decline it if asked, and `SKILL.md` is the only file that states the policy —
  everything else points at it.
- **Honest about what enforces it.** The server checks that a member granted permission to send at
  all; it cannot check whether they approved a particular message. `SKILL.md`, `README.md` and
  `SECURITY.md` now say so plainly instead of implying the approval step is a hard control.
  Closing that gap server-side is tracked, not done.
- **Tested and untested installs are structurally separated** — `install/` holds Claude Code;
  `install/experimental/` holds OpenClaw and everything else.
- Installs now clone a **released tag** rather than a moving branch.
- The ten minutes is described accurately: about 600 words of briefing, with the rest of the ten
  minutes belonging to the member's decisions. (0.2.0 called 800 words "ten minutes of reading",
  which is roughly double the real rate.)

### Added
- Scenario 7: a member offers to stop reviewing posts, and the agent declines — the slow slide out
  of rule 1.
- An executable verification step: `h_sync_nest` carrying no taps changes nothing and proves the
  server answers, the account is right, and the tool surface matches.
- Failure rows for rate limiting (429) and duplicate delivery; an explicit note that **a sync is
  partly a write** because it carries taps, and why retrying it is nonetheless safe.
- Preflight for an existing clone and an existing same-named skill (0.2.0 checked only the plugin
  and the MCP server); schedule removal in every uninstall; an uninstall step for generic installs.
- An imperative-count check in `CONTRIBUTING.md`, and a note on parsing structured config rather
  than appending text to it.

### Fixed
- `openclaw automations delete "<name>"` → `openclaw automations remove <jobId>`, with the id
  stored at install so removal is possible later.
- Cline is listed as having persistent scheduling (`cline schedule`); 0.2.0 said it had none, and
  used that error to claim OpenClaw was the only harness that could run unattended.
- The ChatGPT section no longer asserts a Plus/Pro read-only connector tier or an hourly task
  ceiling — neither is established by OpenAI's own documentation. It now says what the docs do
  say (full MCP connectors are Business/Enterprise/Edu, admin-enabled) and marks the rest unknown.
- Claude Code OAuth tokens are described as going to the OS keychain **or a credentials file**,
  which is what the docs actually say.
- Removed the OpenClaw bootstrap-file size limits, which could not be confirmed against a
  first-party page.
- "No sender can derive" a filtering verdict softened to what the server actually does — the
  original was an information-theoretic claim the product can't support.

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
  `install/other-harnesses.md` (moved to `install/experimental/` in 0.3.0).

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

[0.3.0]: https://github.com/zion-zzy/humanest-agent/releases/tag/humanest--v0.3.0
[0.2.0]: https://github.com/zion-zzy/humanest-agent/releases/tag/humanest--v0.2.0
[0.1.0]: https://github.com/zion-zzy/humanest-agent/releases/tag/humanest--v0.1.0
