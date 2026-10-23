---
name: px-council-personas
description: The persona library for the px-council framework — a catalog of debater and red-team personas (engineer, skeptical-investor, psychologist, product-leader, entrepreneur, and more) that px-council selects from and px-council-mod runs. Use when the user asks to list, add, edit, or contribute council personas, says "add a persona", "show the council library", "create a new seat", or when px-council needs to recommend or create a roster. Not a debate skill; it only manages the library.
license: MIT
metadata:
  author: prodagenx
  version: "1.0.0"
---

# px-council-personas — Persona library

Files:
- `index.md` — catalog. One row per persona. px-council reads this to recommend rosters.
- `personas/<id>.md` — one file per persona. px-council-mod passes the whole file to the seat.
- `personas/_template.md` — the template for new personas.

## Persona file format

```
---
id: engineer       # lowercase, hyphenated, matches filename; used in roster and log
name: Engineer
role: debater      # debater | red-team
summary: one line, shown in the index
best_for: [requirements, technical risk, build cost]
version: 1
---
<body: lens, what to investigate, starter research leads, required counterpoint>
```

Keep bodies under 250 words. Personas are lenses, not scripts: they say
how to look and what to be suspicious of, never what to conclude.

## Listing

"Show the library": print index.md as a table (id, name, role, summary,
best_for).

## Creating a persona

Triggered by the user, or by px-council when the user requests a seat
that does not exist.

1. Ask for: name, the lens in one sentence, what this seat is uniquely
   suspicious of, and whether it is a debater or red-team seat.
2. Draft `personas/<id>.md` from `_template.md`. Starter research leads
   must be real, named sources the author knows; otherwise write
   "[VERIFY] <topic to look up>". Never invent citations.
3. Show the draft. Save only on approval.
4. Append a row to index.md.
5. Offer the contribution flow below.

## Editing

Read the file, apply the change, bump `version`, update the index row if
summary or best_for changed.

## Contributing to the shared library (GitHub)

After a new persona is saved, ask: "Contribute this persona to the shared
px-council library on GitHub?" The shared library is
https://github.com/prodagenx/px-council. Two paths; offer both and let the
user pick.

**A. Draft only** — write `CONTRIBUTING-<id>.md` in the current working
directory (not inside the skill folder) with: the persona file content,
the index row, and these steps: fork the repo, copy the file into
`skills/px-council-personas/personas/`, add the row to
`skills/px-council-personas/index.md`, open a pull request titled
`persona: <name>`.

**B. Open the PR** — only if the `gh` CLI is installed and authenticated
(`gh auth status`). Fork and clone (`gh repo fork prodagenx/px-council
--clone`) or use the local checkout the user names, create
branch `persona/<id>`, add the two changes, commit `persona: add <name>`,
push, `gh pr create`. Show the PR URL. If any step fails, fall back to A.

Never push without showing the diff and getting a yes.
