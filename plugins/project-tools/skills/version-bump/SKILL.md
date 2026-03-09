---
name: version-bump
description: Detect which plugins changed and bump versions in both plugin.json and marketplace.json
allowed-tools:
  - Read
  - Edit
  - Bash
  - Glob
  - Grep
---

Detect which plugins have uncommitted or recently committed changes and bump their versions in both
`plugin.json` and `marketplace.json`.

## Steps

1. **Detect changed plugins**: Run `git diff --name-only HEAD` and `git diff --name-only --cached` to find
   modified files. Extract the plugin names from paths matching `plugins/<name>/`.

2. **If no changes detected**: Check the last commit with `git diff --name-only HEAD~1 HEAD` and extract
   plugin names from that instead.

3. **Determine bump level** for each changed plugin:
   - **patch** (x.y.Z): skill content edits, bug fixes, wording changes
   - **minor** (x.Y.0): new skills added, new features in existing skills
   - **major** (X.0.0): breaking changes (renamed skills, removed skills, changed behavior)
   - Analyze the nature of changes to pick the right level. When in doubt, use patch.

4. **For each changed plugin**:
   a. Read `plugins/<name>/.claude-plugin/plugin.json` and bump the `"version"` field
   b. Read `.claude-plugin/marketplace.json` and bump the matching plugin's `"version"` field
   c. Ensure both versions match after bumping

5. **Report**: List each plugin bumped, the old version, new version, and bump level.

## Important

- Never bump plugins that have no changes.
- Both version fields (plugin.json and marketplace.json) must always match.
- If a plugin exists in the directory but not in marketplace.json, warn the user instead of crashing.
- Do not commit — only make the version edits.
