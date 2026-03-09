---
name: validate-marketplace
description: Validate that marketplace.json, plugin.json files, and README are consistent and complete
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
---

Validate the integrity and consistency of the marketplace structure.

## Checks

1. **Every plugin directory has a marketplace entry**: Glob for `plugins/*/.claude-plugin/plugin.json` and
   verify each plugin appears in `.claude-plugin/marketplace.json`.

2. **No orphan marketplace entries**: Every entry in `marketplace.json` must have a corresponding
   `plugins/<name>/.claude-plugin/plugin.json` directory.

3. **Versions match**: For each plugin, the version in `plugin.json` must match the version in
   `marketplace.json`.

4. **Every plugin has at least one skill**: Each plugin directory under `plugins/<name>/skills/` must
   contain at least one `SKILL.md` file.

5. **README catalog is complete**: Every plugin in `marketplace.json` must appear in the README.md catalog
   table and have a per-plugin section.

6. **README skill counts are accurate**: The skill count in the catalog table must match the actual number
   of SKILL.md files in each plugin.

7. **SKILL.md frontmatter is valid**: Each SKILL.md must have `name` and `description` in its YAML
   frontmatter.

8. **Naming conventions**: All plugin and skill directory names must be kebab-case.

9. **Alphabetical order**: Plugins in `marketplace.json` should be in alphabetical order (warn if not).

## Output

Print a validation report:
- List each check with PASS or FAIL
- For failures, explain what's wrong and how to fix it
- End with a summary: total checks, passed, failed

## Important

- This skill only reads and reports — it never modifies any files.
- Run all checks even if early ones fail.
