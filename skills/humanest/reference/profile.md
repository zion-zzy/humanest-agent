# The profile — what's public, what isn't, and what it's called

## Public: `name`, `bio`, `links`

This is the whole of what another member sees on your member's card, plus the date they confirmed
it. Draft the bio freely — a few lines saying who they are — but only their confirmation puts a
version in front of anyone. The name comes from signup and is theirs: never overwrite it.

## Private: `now`, `ask`, `offer`, `topics`, `stage`, `region`, `one_line`, `background`

The server shows the content of these to nobody. They do two jobs: they sharpen your filtering,
and they're the material the bio is drafted from.

One honest detail worth knowing, because your member may ask: search matches against `now`, `ask`
and `offer`, so a searcher can learn *which* of those fields matched their query — never what it
says. That's the whole of the leak, and it's the price of being findable at all.

Keep every one of these out of anything you write. They leak only if you put them there, and the
server cannot tell your prose from a disclosure.

## The names matter

The schema uses its own words, and an agent that guesses at them fails validation:

| What your member calls it | The field |
|---|---|
| what I'm working on | `now` (a summary, and since when) |
| what I want / what I'm looking for | `ask` (each one with an expiry) |
| what I can help with | `offer` |
| my one-liner | `one_line` |
| where I've been | `background` (optional) |

Plus `topics`, `stage`, `region` — which say what they sound like.

## Keeping it current

**Notice staleness yourself.** Watch the confirmation date, and watch for your member telling you
something the profile doesn't know — a new project, a job change, an ask that's been answered.
Don't wait for a prompt from the server; it may never come.

Then it's two minutes of questions, the values shown back, and a save. **Every
`h_update_profile` call carries something they just agreed to**, public or private — the private
fields are theirs too, and a wrong one quietly distorts everything you filter afterwards.
