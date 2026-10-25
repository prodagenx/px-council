# px-council

A persona council for Claude. Give it a claim, and a council of expert personas debates it under strict evidence rules: openings, cross-examination, a pre-mortem, a majority vote on every contested claim, and a written synthesis that keeps the dissent.

By [prodagenx](https://github.com/prodagenx). MIT licensed.

## How a council runs

1. **Setup** (`/px-council`): a short questionnaire captures the subject, the thesis, the audience, the output you want, and how strict the evidence must be. It is saved as a context file every seat reads.
2. **Roster**: pick 3 or 5 personas from the library (odd, so votes resolve by majority), or create a new one.
3. **Debate** (`px-council-mod`): each seat runs as an isolated subagent.
   - Round 1, opening positions, every claim tagged `[STUDY]`, `[INDUSTRY]`, `[CASE]`, or `[REASONING]`
   - Round 1.5, the Advocate probes each seat
   - Round 2, cross-examination in a cycle, with concessions recorded
   - Round 3, pre-mortem red team
   - Round 4, vote on every claim that was attacked and not conceded
   - Round 5, synthesis into a memo, then the Advocate's dissent appended unedited
4. **Output**: a document with surviving claims, vote tallies, "what we no longer claim", open `[VERIFY]` items, and the evidence to gather next.
5. **Executive summary** (`/px-council`, Step 6): each seat scores both options against weighted criteria in a logged scoring round, and the Advocate reviews the scores. The summary covers:
   - each seat's pro, con and key condition
   - a decision matrix with an overall rating per option
   - a decision: proceed if the leader's margin clears a configurable threshold (`decision_margin_pct`, default 10%), otherwise defer
   - next steps
   - a glossary that explains every claim, round and evidence ID

The **Advocate** (`px-council-adv`) sits on every council outside the seat count and never votes. Its job is to find the weakest claim and say whether it would sign the result.

Seats are told never to invent a source: an uncertain citation is written `[VERIFY]`, and those items are listed in the final document for you to check.

## Skills

| Skill | Does |
|---|---|
| `px-council` | Entry point and controller: questionnaire, saved context file, roster, hand-off, executive summary |
| `px-council-mod` | Moderator: runs the rounds, keeps the log, writes the document |
| `px-council-adv` | The Advocate: probes, pre-mortem, dissent |
| `px-council-personas` | The persona library: list, add, edit, contribute |

**About the `px-` prefix:** every prodagenx skill starts with `px-`, so prodagenx skills sort together in `.claude/skills/` and are easy to tell apart from skills you get elsewhere. The four council skills refer to each other by these names, so keep the prefix if you install them by copying the folders.

## Personas (24)

psychologist, product-leader, entrepreneur, engineer, skeptical-investor (red team), senior-architect, ceo, cfo, cio, cpo, caio, senior-frontend-developer, expert-dba, senior-data-analyst, senior-backend-developer, creative-director, cx-lead, target-customer, ciso, legal-compliance, sales-leader, coo, quality-engineer (red team), vp-engineering.

See [`skills/px-council-personas/index.md`](skills/px-council-personas/index.md) for each persona's lens and best uses.

## Install

**Claude Code plugin**

```
/plugin marketplace add prodagenx/px-council
/plugin install px-council@prodagenx
```

Plugin skills are namespaced, so the entry point may appear as `/px-council:px-council`.

**Copy the skills**

Copy the four folders in `skills/` into `~/.claude/skills/` (all projects) or `.claude/skills/` in one project. Keep them side by side; the entry skill finds the persona library in the sibling folder. Then run `/px-council`.

**Claude.ai**

Zip each folder in `skills/` and upload it under Settings, Capabilities, Skills. Claude.ai has no subagents, so the moderator runs each seat as a separate labelled message and tells you that seat independence is weaker.

## Where files go

On first run you choose a folder for context files, logs, and memos (default `./.px-council/`). They can hold confidential material, so add the folder to `.gitignore` in any repository you work in.

## Add or contribute a persona

Say "add a persona", or ask for one during roster selection. The library skill drafts it from the template, saves it on approval, and offers to open a pull request here. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Disclaimer

Persona seats such as CFO, Legal & Compliance, and CISO are lenses for stress-testing an argument. Their output is not legal, financial, security, or other professional advice. Check every cited source before relying on it.

## License

[MIT](LICENSE) © 2026 prodagenx
