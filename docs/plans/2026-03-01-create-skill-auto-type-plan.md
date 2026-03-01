# Create-Skill Auto Type Detection — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Modify the `create-skill` skill to auto-determine whether to scaffold a skill or command based on the audit-skill-type criteria, with user override.

**Architecture:** Single-file rewrite of `SKILL.md` that adds a type-determination step between description gathering and scaffolding. The 5 audit criteria are inlined directly. Two scaffold templates (skill and command) replace the current single template.

**Tech Stack:** Markdown skill definitions (no build system, no tests)

---

### Task 1: Rewrite create-skill SKILL.md

**Files:**
- Modify: `plugins/project-tools/skills/create-skill/SKILL.md` (entire file)

**Step 1: Replace the SKILL.md with the updated version**

Write this exact content to `plugins/project-tools/skills/create-skill/SKILL.md`:

````markdown
---
name: create-skill
description: Scaffold a new skill or command with correct structure, auto-determining the appropriate type
argument-hint: "<name> [description]"
allowed-tools:

- Read
- Write
- Edit
- Glob
- Bash

---

Scaffold a new Claude Code skill or command and register it in the README. Automatically determines whether the
addition is better suited as a **skill** (rich multi-step workflow) or a **command** (lightweight single action)
based on the audit criteria below, then asks the user to confirm or override before scaffolding.

- **$0** is the name in kebab-case (e.g., `create-pr`)
- **$1** (and everything after the name) is an optional one-sentence description

If `$ARGUMENTS` is empty, ask the user for the name and description before proceeding.

## Steps

1. **Validate the name**:
    - Confirm `$0` is kebab-case (lowercase letters and hyphens only). If not, convert it and tell the user.
    - Check that neither `skills/$0/` nor `commands/$0.md` already exists in the target plugin. If either does, stop and
      tell the user it already exists.

2. **Read existing files for reference**: Read one or two existing `SKILL.md` files and one command `.md` file (if any
   exist) from the plugin directory to confirm current conventions before writing anything.

3. **Determine the description**: Use `$1` if provided. If not, ask the user for a one-sentence description before
   continuing.

4. **Determine the type** — evaluate the description against these five signals:

    | Signal | Favors Skill | Favors Command |
    |--------|-------------|----------------|
    | Step count | Would need 3+ steps with branching or conditional logic | Would need 1–2 simple linear steps |
    | Context needs | Requires domain knowledge or codebase understanding | Needs only file paths or simple args |
    | Tool variety | Would use 3+ different tools | Would use mainly Bash or Write alone |
    | Output complexity | Produces structured analysis, reports, or multi-file changes | Produces a single file or simple output |
    | Bundled resources | Would benefit from scripts/, references/, or assets/ | None needed |

    - Count how many signals favor skill vs command.
    - **Majority wins**; ties default to **skill**.
    - Present the recommendation to the user with a one-line justification per signal.
    - Ask the user to confirm or override the recommendation before continuing.

5. **Scaffold based on determined type**:

    **If skill** — create `<plugin>/skills/$0/SKILL.md` with this structure:

    ```markdown
    ---
    name: <name>
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
    ```

    - Set `name` to `$0` and `description` to the agreed description.
    - Set `argument-hint` only if the skill takes arguments; remove the field entirely if it takes none.
    - Remove tools from `allowed-tools` that the skill clearly won't need.
    - Leave the Steps and Important sections as explicit placeholders — do not invent steps.

    **If command** — create `<plugin>/commands/$0.md` with this structure:

    ```markdown
    ---
    name: <name>
    description: <description>
    argument-hint: "[arguments]"
    allowed-tools:

    - Write

    ---

    <One sentence explaining what this command does.>

    - Bullet point describing key behavior.
    - Another bullet point.
    ```

    - Set `name` to `$0` and `description` to the agreed description.
    - Set `argument-hint` only if the command takes arguments; remove the field entirely if it takes none.
    - Include only the tools the command actually needs in `allowed-tools` (commands typically use 1–2).
    - Leave the bullet points as explicit placeholders — do not invent behavior.

6. **Update `README.md`**: If a `README.md` exists in the repo root, add a row to the appropriate per-plugin table:
    - For skills: `| \`/<name> [args]\` | <description> |` under a **Skill** column header
    - For commands: `| \`/<name> [args]\` | <description> |` under a **Command** column header
    - Update the plugin catalog table's skill count if needed (e.g., "5" → "5 skills, 1 command").

7. **Report**: Tell the user what was created, where, and which type. For skills, remind them to fill in the Steps and
   Important sections. For commands, remind them to fill in the bullet points.

## Important

- Never overwrite an existing skill or command — check first and stop if it already exists.
- Do not invent behavior for placeholders. The scaffolded steps/bullets are intentional placeholders.
- Mirror the whitespace, heading style, and frontmatter format exactly as found in existing files.
- If the skills/commands directory is somewhere other than the expected location, infer the correct path from where
  existing files are located.
- The type determination is a recommendation, not a mandate — always let the user override.
````

**Step 2: Verify the file was written correctly**

Run: `head -5 plugins/project-tools/skills/create-skill/SKILL.md`
Expected: The YAML frontmatter opening with the new description containing "skill or command".

---

### Task 2: Bump version in plugin.json

**Files:**
- Modify: `plugins/project-tools/.claude-plugin/plugin.json`

**Step 1: Update the version and description**

Change version from `"1.2.0"` to `"1.2.1"` and update the description to reflect the new capability.

```json
{
  "name": "project-tools",
  "version": "1.2.1",
  "description": "Suggest features, suggest plugins, draft commits, scaffold new skills or commands, and audit skill types",
  "author": {
    "name": "mattbobambrose"
  }
}
```

---

### Task 3: Bump version in marketplace.json

**Files:**
- Modify: `.claude-plugin/marketplace.json`

**Step 1: Update the project-tools entry**

Find the `project-tools` entry and change:
- `"version"` from `"1.2.0"` to `"1.2.1"`
- `"description"` to `"Suggest features, suggest plugins, draft commits, scaffold new skills or commands, and audit skill types"`

---

### Task 4: Update README.md

**Files:**
- Modify: `README.md`

**Step 1: Update the create-skill row in the project-tools table**

Change the `/create-skill` row description from:
```
| `/create-skill <skill-name> [description]` | Scaffold a new skill with correct frontmatter and structure, and add it to the README |
```
to:
```
| `/create-skill <name> [description]` | Scaffold a new skill or command with correct structure, auto-determining the appropriate type |
```

**Step 2: Update the project-tools description in the catalog table**

Change:
```
| `project-tools` | Suggest features, suggest plugins, draft commits, scaffold new skills, and audit skill types | 5 | productivity |
```
to:
```
| `project-tools` | Suggest features, suggest plugins, draft commits, scaffold new skills or commands, and audit skill types | 5 | productivity |
```

**Step 3: Update the project-tools section heading description**

Change:
```
Suggest features, suggest plugins, draft commits, scaffold new skills, and audit skill types.
```
to:
```
Suggest features, suggest plugins, draft commits, scaffold new skills or commands, and audit skill types.
```

---

### Task 5: Commit

**Step 1: Stage all changed files**

```bash
git add plugins/project-tools/skills/create-skill/SKILL.md \
       plugins/project-tools/.claude-plugin/plugin.json \
       .claude-plugin/marketplace.json \
       README.md
```

**Step 2: Commit**

```bash
git commit -m "feat: add auto skill/command type detection to create-skill

Evaluates the user's description against 5 audit criteria (step count,
context needs, tool variety, output complexity, bundled resources) to
recommend skill vs command, with user override. Adds command scaffolding
template alongside the existing skill template."
```
