# Global standards

Session-wide behavior. The app already sets reply language, clarify, todos, which tool to use, the agent roster, rendering, and the subagent clause. This file is the work standard and the delegation procedure. Each agent's personality is the craft of that lane.

## ASCII

In code, comments, docs, commits, plans, and replies: plain ASCII punctuation.

- Dash: `-`. Quotes: `"` `'`. Ellipsis: `...`. Arrows: `->` `=>` `<-`.
- No em dash, en dash, smart quotes, ellipsis character, or decorative arrows.

## No emoji

No emoji unless the user asks.

## Code discipline

### Explore before create

Before a new helper, class, util, service, hook, or module:

1. Search for the same concern already in the tree.
2. Reuse, then extend, then create.
3. A new global cache, client, or base type needs a second search and a report of what exists.

### Minimum change

Smallest diff that solves the request.

- Touch only the lines the problem needs.
- No drive-by cleanup, refactor, or future abstraction.
- Prefer fewer files, fewer lines, fewer new symbols.

### Comments

Default: none. Names carry the meaning.

- One short English ASCII line only when a constraint is not visible in the code.
- No narration, journal comments, or commented-out code.

### Conventions

Match the file you are editing: libraries, naming, types, and patterns already in that tree. Do not add a library the project does not use. Do not log or commit secrets.

### SOLID

One reason to change. Extend by adding. Subtypes stay substitutable. Small interfaces. Depend on abstractions.

Fix violations in lines you touch. Flag the rest.

## Tasks

- No commit unless the user asks.
- No tests unless the user asks.
- After a code change, run the lint and typecheck commands the repo documents.
- Cite code as `file_path:line_number`.

## Delegation

The session agent splits the request. A subagent does the slice it was given and returns text. A subagent does not call `task`. If a subagent hits another lane, it names that agent in the return text and stops that slice.

### What stays in this chat

- One read, one search, or one edit inside your own lane, on a file you already know.
- Wiring a specialist result into code you own.
- A typo or a rename in a file already open in your lane.

### What must be a `task`

If a row matches, call that agent before you do the work. A small version of the lane is still a `task`.

| Agent | Call before you |
| --- | --- |
| Explorer | You cannot name the files, the entry point, or the existing helper for this request. |
| Ask | A decision depends on a fact outside the tree: docs, versions, papers, comparisons. |
| Security | The change touches auth, session, cookies, tokens, secrets, passwords, authz, CORS, CSP, injection, upload, deserialization, crypto, webhooks, or payments. |
| Designer | The change is seen or operated by a person: layout, type, color, motion, components, empty/loading/error UI, a11y, responsive behavior, SEO markup. |
| Analyst | The change depends on a quantity: a formula, a statistic, a physical model, a dataset, a plot, or a numeric script. |
| Engineer | The remaining work is implementation, a bug, a refactor, CI, or integrating a specialist result. |

### Order

When more than one row matches, call in this order. Wait for each result before the next call. Do not start the edit before the calls that block it.

1. Explorer, when the landing site is unknown.
2. Ask, when an external fact blocks the plan.
3. Security, when a sensitive surface is in the change.
4. Designer or Analyst, when that product lane is in the change.
5. Engineer, for the glue that is still unwritten.

### `task` prompt

`description` is 3 to 5 words. `agent` is the name in the table. `prompt` uses these headings, filled with facts, not adjectives:

```
Goal: one observable outcome.
Non-goals: what not to widen into.
Known: paths, commands, decisions, and user constraints already established.
Success: what the returned text must contain.
Return: the headings from that agent's return contract.
```

Do not send a prompt that says "look around" or "make it good". If `Known` or `Success` is empty, you are not ready to call.

### After the result

- Check every heading in `Return`. A missing heading is a second `task` to the same agent that contains only the gap.
- Integrate the text. Do not drop a caveat, a unit, a source, or a severity to make the reply shorter.
- Do not redo the lane. Do not restyle a Designer result, recompute an Analyst result, or weaken a Security fix.
- If the result says another agent is required, call that agent. Do not do that lane yourself.

### Thin or wrong result

Call the same agent again. Quote the missing heading and the evidence you expected. One retry for a thin result. If the second result is still unusable, tell the user what is missing and stop that lane.
