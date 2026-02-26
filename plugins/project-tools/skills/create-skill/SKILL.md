---
name: create-skill
description: Scaffold a new skill with correct frontmatter and structure, and add it to the README
argument-hint: "<skill-name> [description]"
allowed-tools:

- Read
- Write
- Edit
- Glob
- Bash

---

Scaffold a new Claude Code skill and register it in the README.

- **$0** is the skill name in kebab-case (e.g., `create-pr`)
- **$1** (and everything after the name) is an optional one-sentence description

If `$ARGUMENTS` is empty, ask the user for the skill name and description before proceeding.

## Steps

1. **Validate the name**:
    - Confirm `$0` is kebab-case (lowercase letters and hyphens only). If not, convert it and tell the user.
    - Check that `~/.claude/skills/$0/` does not already exist. If it does, stop and tell the user the skill already
      exists.

2. **Read an existing skill for reference**: Read one or two `SKILL.md` files from `~/.claude/skills/` to confirm the
   current conventions (frontmatter fields, section headings, style) before writing anything.

3. **Determine the description**: Use `$1` if provided. If not, ask the user for a one-sentence description before
   continuing.

4. **Create the skill directory and `SKILL.md`**:

   Create `~/.claude/skills/$0/SKILL.md` with the following structure:

   ````markdown
   ---
   name: <skill-name>
   description: <description>
   argument-hint: "[arguments]"
   allowed-tools:

   - Bash
   - Read
   - Write
   - Edit
   - Glob
   - Grep

   ---

   <One paragraph explaining what this skill does and when to use it.>

   ## Steps

   1. **Step one**: Description of the first step.

   2. **Step two**: Description of the second step.

   3. **Step three**: Description of the third step.

   ## Important

   - Key constraint or safety rule.
   - Another constraint.
   ````

    - Set `name` to `$0` and `description` to the agreed description.
    - Set `argument-hint` only if the skill takes arguments; remove the field entirely if it takes none.
    - Remove tools from `allowed-tools` that the skill clearly won't need.
    - Leave the Steps and Important sections as explicit placeholders — do not invent steps for a skill whose behavior
      hasn't been specified yet.

5. **Update `README.md`**: If a `README.md` exists in the skills repo root, add a row for the new skill to the Skills
   table in alphabetical order by skill name. Use this column format:
   `| \`<name>\` | \`/<name> [args]\` | <description> |`

6. **Report**: Tell the user the skill was created at `~/.claude/skills/$0/SKILL.md` and remind them to fill in the
   Steps and Important sections before using it.

## Important

- Never overwrite an existing skill directory — check first and stop if it already exists.
- Do not invent behavior for the new skill's Steps section. The scaffolded steps are intentional placeholders.
- Mirror the whitespace, heading style, and frontmatter format exactly as found in existing skills.
- If the skills directory is somewhere other than `~/.claude/skills/`, infer the correct path from where the existing
  SKILL.md files are located.
