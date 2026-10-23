---
name: px-council
description: Entry point for the px-council framework. Runs the shared-context questionnaire, saves it as a reusable markdown file, recommends an odd-numbered roster (3 or 5) of personas from the px-council-personas library, lets the user confirm, override, or create a new persona, then hands off to px-council-mod, which always runs the standing Advocate (px-council-adv) alongside the seats. Use whenever the user says /px-council, "run the council", "convene a council", "debate this thesis", "stress-test this idea with personas", or wants multiple expert viewpoints to argue a claim and produce an evidence-backed output. Always run this before px-council-mod; never start the moderator without a saved context file.
license: MIT
metadata:
  author: prodagenx
  version: "1.0.0"
---

# px-council — Council entry point

You are starting a council run. Your job in this skill is setup only:
collect the shared context, build the roster, verify, hand off. The
moderator (px-council-mod) runs the debate. Do not debate here.

Library location: the `px-council-personas/` folder beside this skill's
folder (`../px-council-personas/` from this SKILL.md: index.md + personas/).
This holds for personal, project, and plugin installs. If it is not there,
try `~/.claude/skills/px-council-personas/`.

## Step 1 — Find or set the context location

1. Read `~/.claude/px-council/config.md` if it exists. It stores
   `context_dir:` — where context files are saved.
2. If it does not exist, ask once:
   "Where should council context files be saved?" Offer:
   - the current working directory (`./.px-council/`); if that directory
     is inside a git repository, suggest adding `.px-council/` to
     `.gitignore`, since context files and logs may be confidential
   - a path the user names
   Write the answer to `~/.claude/px-council/config.md` as:
   ```
   context_dir: <path>
   ```
   Tell the user it is remembered and where to change it.

## Step 2 — Reuse check

List `*.md` files in `context_dir`. If any exist, show each file's
`subject:` and `thesis:` from its frontmatter and ask:
- Reuse one as-is
- Reuse one but update some answers
- Start fresh

Reuse skips to Step 4 with that file. Update runs Step 3 only for the
fields the user names.

## Step 3 — Questionnaire

Ask in three short groups, not all at once. Accept short answers; ask a
follow-up only when an answer is too vague for a persona to argue from.
Fill `templates/context-template.md` with the answers.

**Group A — Subject**
1. What is being debated? (company, product, decision, or idea; one or two sentences)
2. Stage or status (idea, prototype, pilot, revenue, in production, etc.)
3. Who is the output meant to persuade or inform?

**Group B — Thesis**
4. The core claim in one sentence. Help sharpen it if needed; a council
   needs a claim that can be wrong.
5. Any terms that must be used consistently (optional). Offer the
   defaults in the template if the subject is product-related.
6. Evidence already in hand (demos, pilots, metrics, user quotes).

**Group C — Run settings**
7. Output type: thesis memo, pitch narrative + objections, requirements
   principles, ranked claims with citations, or something else.
8. Evidence bar: peer-reviewed first / mix of studies and industry data /
   most persuasive.
9. Council size: 3 or 5. Odd only, so contested claims resolve by
   majority. Default 5. Explain the rule in one sentence if asked.

Save the filled template as
`<context_dir>/<slug>-context.md` (slug from the subject). Show the
file path and a 5-line summary. Ask for confirmation before continuing.

## Step 4 — Roster

1. Read `px-council-personas/index.md`.
2. Recommend exactly N personas (N from question 9). For each, give one
   sentence on why it fits this thesis. Remind the user that the Advocate
   (`px-council-adv`) runs on every council outside the count and never
   votes, so a seated `role: red-team` persona (for example
   `skeptical-investor`) is optional: recommend one only when the
   audience makes its domain objections valuable.
3. The user may:
   - **Confirm** the recommendation.
   - **Override** by naming other personas from the index.
   - **Request a new persona.** Follow "Creating a persona" in
     `px-council-personas/SKILL.md`: draft it from `personas/_template.md`,
     show it, save it on approval, add a row to index.md, then offer the
     GitHub contribution flow described there.
   Keep N odd. If the user insists on an even number, explain that the
   moderator will break ties by evidence weight, then proceed.

## Step 5 — Verify and hand off

Display:
- Context file path
- Thesis (verbatim)
- Output type and evidence bar
- Roster: persona id, name, role, file path
- Standing: Advocate (px-council-adv) — always on, not counted, no vote

Ask: "Run the council with this setup?" On yes, invoke `px-council-mod`
and pass it the context file path and the list of persona file paths.
On no, return to whichever step the user wants to change.

## Rules

- Never fabricate questionnaire answers. Blank is better than guessed.
- Never start the debate in this skill.
- Keep the user's thesis wording; sharpen only with their agreement.
