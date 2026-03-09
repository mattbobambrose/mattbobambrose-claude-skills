---
name: rename-plugin
description: Rename a plugin across all marketplace files including directory, catalog, and README
argument-hint: "<old-name> <new-name>"
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
---

Rename a plugin across all marketplace files in a single operation.

## Steps

1. **Validate inputs**: Confirm the old plugin name exists in `plugins/<old-name>/` and that the new name
   is kebab-case and doesn't already exist.

2. **Rename the directory**:
   ```bash
   mv plugins/<old-name> plugins/<new-name>
   ```

3. **Update plugin.json**: Edit `plugins/<new-name>/.claude-plugin/plugin.json` to change the `"name"`
   field to the new name.

4. **Update marketplace.json**: Edit `.claude-plugin/marketplace.json` to update:
   - The `"name"` field
   - The `"source"` path to `./plugins/<new-name>`
   - Bump the version (patch)

5. **Update README.md**:
   - Update the catalog table row (plugin name, install command if present)
   - Update the per-plugin section heading
   - Update the install command in the per-plugin section
   - Update any other references to the old name

6. **Check for other references**: Grep the entire repo for the old plugin name and update any remaining
   references (CONTRIBUTING.md, CLAUDE.md, CHANGELOG.md, etc.).

7. **Verify**: Run a final grep for the old name to confirm no references remain.

8. **Report**: Confirm the rename and list all files that were modified.

## Important

- This skill does NOT commit changes — the user should review and commit afterward.
- If any file references the old name in a way that's ambiguous (e.g., substring of another name),
  warn the user rather than making an incorrect replacement.
- Bump the version because the plugin name change affects how users install/update it.
