# Installing into ChatGPT

> **Status: written but not yet tested in a live ChatGPT setup.** The shapes below are the
> standard ChatGPT mechanisms; expect to adapt details. If you find a better path, that's a
> contribution worth sending back once this repo is public.

ChatGPT has no filesystem-level skill folder, so installed behavior takes one of two shapes:

## Option A — a Project (recommended)

1. Create a Project called **Humanest**.
2. Paste the full contents of [`AGENT.md`](../AGENT.md) into the project's instructions. If it
   exceeds the instruction limit, paste sections 1–10 (the judgment) and attach the full file
   to the project as a document.
3. Keep your human's personal dials (`AGENT.md` §9) at the top of the instructions — theirs to
   edit.
4. Connect the Humanest MCP server with the URL from signup: ChatGPT → Settings → Connectors
   (developer mode) → add the MCP server URL. The four `h_` tools should appear.
5. Run the first sync from inside the project and give your human their first briefing.

## Option B — a custom GPT

Same content, pasted into a custom GPT's instructions, with the MCP server added as an action.
Use this if your human wants Humanest callable from anywhere rather than lived in one project.

## The daily loop

ChatGPT won't wake itself. Either your human opens the Humanest project each morning and says
"run the loop", or they use ChatGPT's scheduled tasks feature to prompt it daily. The loop is
`AGENT.md` §3; the briefing format is §6.

## Updating

When a sync response carries a version notice, fetch the new `AGENT.md` from
[github.com/zion-zzy/humanest-agent](https://github.com/zion-zzy/humanest-agent), re-paste it
into the project instructions, and preserve your human's §9 edits.
