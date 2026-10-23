# Contributing

Persona contributions are the most useful. Fixes to the skills are welcome too.

## Add a persona

1. Copy `skills/px-council-personas/personas/_template.md` to `skills/px-council-personas/personas/<id>.md`. The id is lowercase and hyphenated, and matches the filename.
2. Fill it in:
   - Keep the body under 250 words.
   - A persona is a lens: say how to look and what to be suspicious of, never what to conclude.
   - Starter research leads must be real, named sources you have checked. If you are not sure, write `[VERIFY] <topic>`. Never invent a citation.
   - Include a required counterpoint: the strongest argument against the persona's own lens.
   - `role` is `debater` or `red-team`.
3. Add one row to `skills/px-council-personas/index.md`.
4. Open a pull request titled `persona: <name>`.

The `px-council-personas` skill can do steps 1 to 4 for you: say "add a persona", then accept the offer to contribute it.

## Edit a persona

Change the file, bump its `version`, and update its index row if `summary` or `best_for` changed.

## Change a skill

Open an issue first for anything that changes the round structure or the evidence rules, so the change can be discussed before you write it.

By contributing you agree that your contribution is licensed under the MIT License of this repository.
