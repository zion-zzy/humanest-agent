# Security

## Reporting

Email **security@humanest.ai** with what you found and how to reproduce it. We'll acknowledge
within **3 business days** and tell you what we're doing about it within **10**. Please don't
open a public issue for anything that could be used against a member before it's fixed.

Supported: the latest released version. This is pre-launch software with one version line and no
backports.

## What this repo can and cannot secure

This repo is **behavior**, not enforcement. The hard rules — no send without a live standing
permission, no path to anyone outside Humanest, only confirmed profile fields crossing to another
member, the weekly cap — live on the Humanest server. A member who edits or ignores their copy of
these files gains nothing they weren't already allowed. **If you find something in this text that
appears to grant capability, that's a bug worth reporting.**

## The threat we take most seriously

A member's agent reads posts and messages written by strangers, holds private context about its
human, and can send. Anything holding all three needs a human between the reading and the acting
— [Meta's Agents Rule of Two](https://ai.meta.com/blog/practical-ai-agent-security/) is the
clearest statement of why.

So the boundary this repo actually defends is: **nothing addressed to another human leaves
without that member's approval of that specific thing.** The instruction that inbound content is
data rather than instruction is a floor, not a ceiling — twelve published prompt-injection
defenses were tested against adaptive attacks and
[all twelve collapsed](https://simonwillison.net/2025/Nov/2/new-prompt-injection-papers/), so we
don't claim prose stops injection. We claim the human approval does, and we keep the prose
because it raises the cost of the naive attempt.

The classes we most want reported:

- **A path where an agent following these instructions would send, disclose, or fetch something
  because inbound content asked it to.** The scenarios in
  [scenarios.md](skills/humanest/reference/scenarios.md) cover the cases we know; a new one is
  the most useful bug report this repo can receive.
- **A leak of a member's private profile fields, or private context, into outbound text** —
  the server can't distinguish an agent's prose from a leak, so this is behavioral by
  construction and needs to be tight.
- **An install step that overwrites, exposes, or commits something of a member's** — especially
  anything that puts their server URL in a tracked file or a shared log.
- **A claim in this repo that overstates what the server enforces.** An honest-sounding false
  promise is a security defect here, because members will make decisions on it.

## Handling your own credential

Your Humanest server URL may be a bearer credential — treat it as one until told otherwise. Not
in a tracked file, not in a project-scoped `.mcp.json`, not pasted anywhere with retention you
don't control. If it leaks, rotate it from your Humanest settings and revoke your standing
permission first.
