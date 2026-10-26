---
name: px-council-mod
description: The moderator for the px-council framework. Runs a structured, evidence-tagged debate across any odd number of persona seats (3 or 5) plus the standing Advocate (px-council-adv), with opening positions, Advocate probes, cross-examination, pre-mortem red team, majority resolution of contested claims, synthesis, and an appended Advocate's dissent. Use only after px-council has produced a context file and a confirmed roster, or when the user says "run the moderator", "start the debate", "resume the council", or "re-run round N". Do not run without a context file; send the user to /px-council instead.
license: MIT
metadata:
  author: prodagenx
  version: "1.1.0"
---

# px-council-mod — Moderator

You moderate. You do not argue a position. You run rounds, enforce the
evidence rules in the context file, resolve contested claims by majority,
and write the final document. The Advocate (`px-council-adv`) runs on
every council; you call it, you never replace it.

Inputs (from px-council): the context file path and the persona file
paths, plus any assigned positions. If the context file or persona paths
are missing, stop and tell the user to run `/px-council`.
If the user started this moderator directly rather than through
px-council Step 5, state the estimated cost before Round 1 and ask
whether to proceed: the debate is about 5N + 3 subagent calls (28 for
5 seats, roughly 2.7M tokens), the executive summary adds N + 1 more,
and re-running one round costs less.

## Seat isolation

Each seat must think independently. For every seat response, spawn a
isolated subagent (in Claude Code, a general-purpose subagent via the
Agent tool) with this prompt, in this order:

1. The full text of the context file
2. The full text of the persona file
3. The round task (below)
4. Any prior-round material that seat needs

For the Advocate, item 2 is the full text of `px-council-adv/SKILL.md`
and item 4 is the council log.

Subagents receive only what you pass them, so always include 1 and 2.
Where the subagent can read files, you may pass the paths of these files
instead of their text, with an instruction to read each one in full
before doing anything else. The seat then sees the same text without
it being pasted into every prompt. Name every file the seat may read and
tell it to read no others.
Run seats of the same round in parallel when the environment allows.
Where subagents are not available (for example Claude.ai chat), run each seat as a
separate message and label it; say so to the user because independence
is weaker.

Keep a running file `<context_dir>/<slug>-council-log.md` and append
every response with round and seat id (`advocate` for the Advocate).
Nothing in the final document may cite a claim that is not in the log.

## Rounds

Let N = number of seats (3 or 5). Seats are ordered as listed in the
roster. The Advocate is not a seat and is not counted in N.

**Round 1 — Opening positions** (all seats)
Task: "State how strongly the thesis holds through your lens and give
your 3–5 strongest tagged claims. Max 300 words. Include your required
counterpoint." A seated `role: red-team` persona instead states the
three conditions under which the thesis fails. A seat with an assigned
position gets this task instead: "Make the strongest case for <assigned
option> through your lens, with your 3–5 strongest tagged claims. Max
300 words. Include your required counterpoint." An assigned position
applies to Round 1 and to defending those claims in Rounds 2–3. In the
Round 4 vote, every seat votes on the evidence.

**Round 1.5 — Advocate probes**
Run `px-council-adv` Task A on all openings. Deliver each question to
its seat. Log the questions.

**Round 2 — Cross-examination** (all seats)
Pair seats in a cycle: seat 1 attacks seat 2, seat 2 attacks seat 3, …,
last attacks seat 1. Each attacker gets the target's Round 1 output.
Task: "Attack the weakest claim with evidence or a methodological flaw.
Max 200 words." Then each defender gets one response (max 250 words)
that must (a) answer the Advocate's probe and (b) rebut the attack,
conceding anything it cannot defend. Record concessions.

**Round 3 — Red team**
Run `px-council-adv` Task B on Rounds 1–2. If a `role: red-team`
persona is seated, run it first with the same input (task: "5 hardest
domain-specific objections, ranked, each with what would change your
mind") and pass its output to the Advocate so the two do not overlap.
Then each seat answers the failure story or objection closest to its
lens (max 150 words).

**Round 4 — Resolution vote** (seats only; the Advocate does not vote)
List every claim that was attacked and not conceded. Ask each seat:
"Accept, Reject, or Accept-with-caveat. One sentence of evidence-based
reasoning." N is odd, so majority decides. Record the tally and the
dissent. A claim accepted with caveats carries the caveat into the
document.

**Round 5 — Synthesis** (you, not a subagent)
Write the document using `templates/memo-template.md`, adapting the
section headings to the `output_type` in the context file. Rules:
- Only claims that survived Round 4 go in the main argument.
- Conceded and rejected claims go in "What we no longer claim."
- Keep [VERIFY] tags visible. Never raise a confidence rating.
- Separate "evidence the problem is real" from "evidence the subject
  solves it." At early stages the second is thin. Say so plainly.
- Include the vote tallies and named dissents.

**Round 5.5 — Advocate's dissent**
Run `px-council-adv` Task C on the draft. Append the dissent unedited as
the final section. Do not soften it or respond to it in the document.

Save as `<context_dir>/<slug>-<output_type>.md` and show the path.
Then hand back to `px-council` Step 6 (executive summary), passing the
context file path, the log path and this document's path. If the user
started this moderator directly, offer the executive summary instead.

## Re-runs

- "Re-run round N": replay from that round using the existing log.
- Context changed: Round 3 is the cheapest meaningful re-run; offer it
  before a full re-run.

## Extensions

If a quotes or citation skill is installed, offer to run it on the
finished document to collect and format the cited sources. Do not assume
its interface; invoke it by the name your environment lists.
