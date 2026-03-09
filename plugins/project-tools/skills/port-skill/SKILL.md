---
name: port-skill
description: Port a skill from an external source into the marketplace with full wiring
argument-hint: "<source-path-or-url> <target-plugin>"
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - WebFetch
---

Port a skill definition from an external source into this marketplace, handling all required wiring.

## Steps

1. **Read the source skill**: Read the SKILL.md from the given path or URL. If it's a URL, fetch it.
   If it's a local path, read it directly.

2. **Validate the source**: Ensure it has valid YAML frontmatter with at least `name` and `description`.

3. **Determine the target plugin**: Use the plugin name provided by the user. If not provided, ask which
   existing plugin it should go into, or whether to create a new one.

4. **Adapt the skill**:
   - Ensure the skill name is kebab-case
   - Ensure the description starts with a verb and is one sentence
   - Preserve all skill logic and steps
   - Adjust any paths or references that are specific to the source project

5. **Create the skill file**: Write to `plugins/<target-plugin>/skills/<skill-name>/SKILL.md`.

6. **Bump versions** (minor bump):
   - Edit `plugins/<target-plugin>/.claude-plugin/plugin.json` version
   - Edit `.claude-plugin/marketplace.json` matching entry version

7. **Update README.md**:
   - Add the skill to the target plugin's skill table
   - Update the skill count in the catalog table

8. **If creating a new plugin**: Also create `plugins/<name>/.claude-plugin/plugin.json`, add the
   marketplace.json entry, and add the README section.

9. **Report**: Confirm what was created and what versions were bumped.

## Important

- Do not modify the skill's core logic — only adapt formatting and naming conventions.
- If the source skill references tools not available in Claude Code, warn the user.
- Always bump versions — never skip this step.
