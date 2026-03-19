---
name: update-project-docs
description: Update README.md, CLAUDE.md, and llms.txt in one pass
allowed-tools:

- Read
- Write
- Edit
- Glob
- Grep
- Bash
- Skill

---

Update all three project-level documentation files in a single pass: `README.md`, `CLAUDE.md`, and `llms.txt`.

## Steps

1. **Run `/update-readme`**: Invoke the `update-readme` skill to analyze the codebase and update `README.md`.

2. **Run `/revise-claude-md`**: Invoke the `revise-claude-md` skill to update `CLAUDE.md` with learnings from the
   current session and codebase state.

3. **Run `/generate-llms-txt`**: Invoke the `generate-llms-txt` skill to generate or update `llms.txt` (and
   `llms-full.txt` if the project is small enough).

4. **Report**: Summarize what changed across all three files. List each file and whether it was created, updated, or
   left unchanged.

## Important

- Run the skills in the order listed above — `README.md` and `CLAUDE.md` are inputs to `llms.txt` generation, so they
  should be current before that step runs.
- If any skill fails or the file it targets does not apply to this project, note it in the report and continue with the
  remaining skills.
- Do not run skills in parallel — run them sequentially so each benefits from the previous step's output.
