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

So the boundary this repo defends is: **nothing reaches another human until that member has seen
the words and agreed to them.** The instruction that inbound content is data rather than
instruction is a floor, not a ceiling — twelve published prompt-injection defenses were tested
against adaptive attacks and
[all twelve collapsed](https://simonwillison.net/2025/Nov/2/new-prompt-injection-papers/) — so we
don't claim prose stops injection. We keep the prose because it raises the cost of the naive
attempt.

**And we won't claim more than that, because the approval step is behavioral too.** Today the
server verifies that a member granted their agent permission to send at all; it does not verify
that the member approved the specific text. An agent that ignores these files can therefore send
something unapproved, and the server will accept it. That is a real gap, it is known, and closing
it — approval bound to the exact content and recipients, checked server-side — is tracked work
rather than finished work. **Anyone reporting a way to make an agent send unapproved content is
reporting a bug in a defense we describe as partial, not defeating one we call complete.**

The classes we most want reported:

- **A path where an agent following these instructions would send, disclose, or fetch something
  because inbound content asked it to.** The scenarios in
  [scenarios.md](skills/humanest/reference/scenarios.md) cover the cases we know; a new one is
  the most useful bug report this repo can receive.
- **A leak of a member's private profile fields, or private context, into outbound text** —
  the server can't distinguish an agent's prose from a leak, so this is behavioral by
  construction and needs to be tight.
- **An install step that overwrites, exposes, or commits something of a member's** — especially
  anything that puts their OAuth token where their harness didn't intend it to go.
- **A claim in this repo that overstates what the server enforces.** An honest-sounding false
  promise is a security defect here, because members will make decisions on it.

## Handling your own credential

**The server address is public** — `https://humanest.ai/mcp`, the same for every member —
so there is nothing sensitive about it and nothing to rotate. What identifies you is the OAuth
token your harness stores after you sign in, and that is the thing worth protecting: keep it
where your harness puts it (an OS keychain where there is one), don't copy it into project files
or logs, and if you think it has been exposed, revoke access from your Humanest settings.
