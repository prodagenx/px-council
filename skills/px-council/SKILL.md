---
name: px-council
description: Entry point and controller for the px-council framework. Runs the shared-context questionnaire, saves it as a reusable markdown file, recommends an odd-numbered roster (3 or 5) of personas from the px-council-personas library, lets the user confirm, override, or create a new persona, then hands off to px-council-mod, which always runs the standing Advocate (px-council-adv) alongside the seats. After the moderator finishes, it writes an executive summary with an ID glossary, each seat's pro/con, a scored decision matrix, an overall rating and decision, and next steps. Use whenever the user says /px-council, "run the council", "convene a council", "debate this thesis", "stress-test this idea with personas", "executive summary of the council", or wants multiple expert viewpoints to argue a claim and produce an evidence-backed output. Always run this before px-council-mod; never start the moderator without a saved context file.
license: MIT
metadata:
  author: prodagenx
  version: "1.2.0"
---

# px-council — Council entry point

You are starting a council run. Your job in this skill is setup and
close-out: collect the shared context, build the roster, verify, hand off,
then write the executive summary (Step 6). The moderator (px-council-mod)
runs the debate. Do not debate here.

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
3. If the thesis compares two options ("X is better than Y"), assign at
   least one seat to argue for each option, choosing seats whose lens
   suits that side, and show the assignment in the recommendation. The
   Advocate attacks claims but never builds the case for the other
   option, so without an assigned seat the losing option is scored only
   on the attacks made against it. For a single-claim thesis, if every
   recommended seat is likely to favour the claim, say so and offer to
   assign one seat to argue against it.
4. The user may:
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
- Roster: persona id, name, role, file path, and assigned position if any
- Standing: Advocate (px-council-adv) — always on, not counted, no vote
- Estimated cost: about 6N + 4 subagent calls for N seats (22 for 3
  seats, 34 for 5), roughly 2M to 3.5M tokens, including the Step 6
  scoring round. See "Cost of a run" in the README.

Ask: "Run the council with this setup?" On yes, invoke `px-council-mod`
and pass it the context file path, the list of persona file paths, and
any assigned positions.
When px-council-mod has saved its document, continue to Step 6.
On no, return to whichever step the user wants to change.

## Step 6 — Executive summary

Run after px-council-mod saves its document, or when the user asks for an
executive summary of an existing run. Fill
`templates/executive-summary-template.md`.

**6a — Settings.** Confirm with the user:
- The two options being compared (from the thesis). If the thesis is a
  single claim rather than a choice, compare "Adopt the claim" against
  "Status quo" (what happens if nothing changes), and name both options
  that way in the summary.
- Criteria and weights (1–5). Propose 6–8 criteria derived from the
  claims that survived the resolution vote, plus one criterion for
  "what works today" so the incumbent option can score for what it
  already delivers. Show each criterion's source claim IDs.
- `decision_margin_pct`: read the context file frontmatter first, then
  `<context_dir>/config.md`, otherwise use 10. Show the value and its
  source.

**6b — Scoring round (Round 6).** If the log has no Round 6 for this run,
run it. Spawn one isolated subagent per seat, in parallel where possible,
with: the context file, the seat's persona file, the moderator's final
document, the full log, and this task:
"Score each criterion 1–5 for each option from your lens, with one
line of evidence per score citing the log or a source. Then give one
sentence Pro (for the claim), one sentence Con (against the claim), and
your one key condition. Do not raise any confidence above what the log
records."
Then run `px-council-adv` Task D (score review) on the scores.
Append all of it to the log as Round 6.

**6c — Compute and write.**
1. Per criterion and option: mean of the seat scores; weighted score =
   mean × weight. Totals per option; maximum = 5 × sum of weights.
2. Margin = leader total − other total. If margin ≥
   `decision_margin_pct`% of the maximum, the decision is "Proceed with
   <leader>", conditional on the conditions that survived the vote.
   Otherwise "Defer pending <the evidence the Advocate named as most
   likely to change the verdict>".
3. Write every section of the template:
   - Use plain language in the body. Put every ID (claim, round,
     failure story, evidence item, risk, use case) in Appendix A.
   - Where the Advocate refused to sign wording, use its corrected
     wording and say so.
4. Save as `<context_dir>/<slug>-executive-summary[-runN].md`. Show the
   path and the decision line.

Verify before showing:
- The body has no unexplained IDs.
- Every score traces to Round 6.
- Totals and margin recompute.

## Rules

- Never fabricate questionnaire answers. Blank is better than guessed.
- Never start the debate in this skill. The Step 6 scoring round is not
  debate: seats score settled claims and do not argue new ones.
- Never fabricate scores. Every number in the executive summary comes
  from the logged scoring round.
- Keep the user's thesis wording; sharpen only with their agreement.
