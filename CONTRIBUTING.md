# Contributing

This repo is instructions an AI agent follows on a real person's behalf. That makes some
contributions unusually valuable and a few unusually risky.

## Most wanted

1. **An install guide for a harness we haven't covered.** Walk it end to end, then write what
   actually happened — including where the guide you followed was wrong. The shape to match is
   [install/claude-code.md](install/claude-code.md): preflight, install, connect, verify,
   schedule, uninstall, troubleshoot.
2. **A scenario that catches a real failure.** [scenarios.md](skills/humanest/reference/scenarios.md)
   is how this skill gets checked instead of admired. A case where a reasonable agent does the
   wrong thing is worth more than a paragraph of advice.
3. **A correction.** If a command here doesn't work, or a claim about a harness is out of date,
   an issue saying so is a real contribution. Two of the three install guides are untested and we
   say so; help us shorten that list.

## Please don't

- **Add length.** Measured research says agent instruction files get *worse* past roughly 150
  lines ([ETH Zurich, arXiv:2602.11988](https://arxiv.org/abs/2602.11988)). `SKILL.md` stays
  under 150; new detail goes in a reference file, exactly one level deep. A PR that grows the
  canonical file needs to say what it's removing.
- **Add a rule without pairing it.** A bare "don't do X" measurably degrades behavior; say what
  to do instead in the same breath.
- **Explain things the model already knows.** Every sentence should change what an agent does.
- **Introduce a second way to say something.** `SKILL.md` is the only normative source; every
  other file points at it rather than restating it. This is not a style rule — v0.2 stated the
  approval policy in four files and they had already drifted into contradiction by the time
  anyone read them together. Where a repeat genuinely earns its place (the README has to describe
  the deal; `SECURITY.md` has to state the threat), it must be checked against `SKILL.md` in the
  same commit that changes either.

## House rules

- The canonical behavior is `skills/humanest/SKILL.md`. Everything else supports it.
- **Never these words**, anywhere: microphone, recording, listening, surveillance, transcription,
  wire, data capture. Humanest doesn't do those things and won't describe itself as if it might.
- The product is **Humanest**. "The nest" names only the live map of members and a member's own
  view of it — never the product itself.
- Nothing security-critical may depend on text in this repo. The server enforces the hard rules;
  a member who edits their copy of these files gains nothing.
- Claims about a harness carry a link to that harness's own documentation, and untested ones say
  they're untested.

## The PR itself

Fork, branch (`fix/openclaw-uninstall`, `add/goose-install`), one concern per pull request. In
the description, say **what you actually ran** — the harness, its version, the commands, and what
happened. For an install guide that matters more than the prose: a guide written from
documentation is worth something, and one written from a real install is worth much more, so say
which yours is.

Expect a review that checks your claims against the harness's own docs, because two rounds of
that is what caught the wrong commands already in here. Maintainers may edit for length —
`SKILL.md` has a hard ceiling and something usually has to leave when something arrives.

To run the behavior scenarios yourself: install the plugin from your working copy with
`claude --plugin-dir .`, then work through
[scenarios.md](skills/humanest/reference/scenarios.md) in a session with no Humanest server
connected — describe each situation, ask what the agent would do, compare against the written
answer. No server needed, and it takes about ten minutes.

## Before you open a PR

```bash
claude plugin validate . --strict     # manifests must pass
wc -l skills/humanest/SKILL.md        # must be under 150

# Imperative load: frontier models track roughly 150-200 instructions before
# compliance degrades, and the harness itself already spends some of that budget.
# Keep the canonical file's count well inside it — under 100 is the working bar.
grep -cE '^\s*[-*0-9.]*\s*\*\*?[A-Z][a-z]+\b|(^|\. )(Never|Always|Don.t|Do not|Keep|Show|Send|Stop|Report|Use|Ask|Tell|Draft|Judge|Sync|Sleep|Read|File|Confirm|Propose|Pick|Treat) ' \
  skills/humanest/SKILL.md
```

Then re-read your diff as an agent following it literally, with no context from this
conversation. Anywhere you'd have to guess is the defect.

Changes to behavior get a `CHANGELOG.md` entry and a version bump in both
`.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`; the two must agree.
