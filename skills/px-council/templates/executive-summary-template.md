---
subject: "[[subject from the context file]]"
thesis_final: "[[final claim wording after the council, using the Advocate's wording where it refused to sign]]"
decision: "[[Proceed with <option>, conditional | Proceed with <option> | Defer pending <evidence>]]"
decision_margin_pct: [[value used]]
decision_margin_source: "[[context file | config.md | default]]"
run: "[[run label, e.g. run 2]]"
created: [[YYYY-MM-DD]]
---

# Executive summary: [[subject in 6–10 plain words]]

Context: [[context file path]] — Log: [[log file path]] — Full output: [[council output file path]]
Council: [[member names]]. Standing: the Advocate (reviews, never scores or votes).

<!-- Body rules:
- Never use a bare ID (V3, C2, F1, B2.4, R1, UC 22, E1) in the body. Write the plain meaning; add the ID in brackets only if a reader needs to look it up, and list it in Appendix A.
- Every score comes from the logged scoring round. Never estimate or round scores up.
- Confidence never exceeds the log.
- Where the Advocate refused to sign wording, use the Advocate's corrected wording.
Delete this comment. -->

## 1. Decision at a glance

| | |
|---|---|
| **Decision** | [[one sentence]] |
| **Overall rating** | [[Option A]]: [[weighted total]] / [[max]] ([[%]]) · [[Option B]]: [[weighted total]] / [[max]] ([[%]]) |
| **Margin** | [[points]] ([[% of max]]) against a threshold of [[decision_margin_pct]]% ([[source]]) |
| **Confidence** | [[Low / Medium / High, as rated in the log]] |
| **Conditions** | [[numbered list of conditions the decision depends on]] |

## 2. The claim in plain language

[[2–3 sentences: what the council concluded, what it did not conclude, and what is demonstrated today vs. planned.]]

## 3. Decision matrix

Scores are 1 (poor) to 5 (strong), the mean of the seated members' scores. Weighted score = mean × weight.

| Criterion | What it means | Weight | [[Option A]] mean | [[Option B]] mean | [[Option A]] weighted | [[Option B]] weighted |
|---|---|---|---|---|---|---|
| [[criterion]] | [[one-line plain meaning]] | [[1–5]] | | | | |
| **Total** | | [[sum of weights]] | | | **[[total]]** | **[[total]]** |

**Decision rule:**
- If the leading option is ahead by at least [[decision_margin_pct]]% of the maximum weighted score ([[max]]), recommend it, together with its conditions.
- If the margin is smaller, defer until [[the single piece of evidence most likely to change the verdict]] arrives.

## 4. Council members: rating, pro and con

| Member | Lens | [[Option A]] total | [[Option B]] total | Pro (for the claim) | Con (against the claim) | Key condition |
|---|---|---|---|---|---|---|
| [[name]] | [[one line]] | | | [[one sentence]] | [[one sentence]] | [[one sentence]] |

### The Advocate's position (no score)

[[3–5 sentences from the Advocate's review of the scoring round and its dissent: what it would sign, what it would not, and why.]]

## 5. Risks and what is not yet proven

| Risk or gap | Plain explanation | What would settle it |
|---|---|---|
| [[name]] | [[one sentence]] | [[one sentence]] |

## 6. Next steps

| # | Action | Owner (role) | Settles |
|---|---|---|---|
| 1 | [[action]] | [[role]] | [[risk, condition or open item, in plain words]] |

## Appendix A — ID glossary

Every ID used in the council's source documents, with its plain meaning.

| ID | Meaning |
|---|---|
| [[ID]] | [[one line]] |

Note: if the run uses "R1, R2…" for rounds and "R1" for a carried risk, label them "Round 1" and "Risk R1" to keep them distinct.

## Appendix B — Source documents

- [[path]] — [[what it is]]
