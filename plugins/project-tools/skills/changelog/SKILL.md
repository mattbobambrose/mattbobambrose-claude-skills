---
name: changelog
description: Generate a changelog from git history grouped by plugin and version
argument-hint: "[plugin-name] [since-tag-or-date]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
---

Generate a changelog from git history, grouped by plugin.

## Steps

1. **Determine scope**:
   - If a plugin name is given, generate changelog for that plugin only.
   - If no plugin name is given, generate changelog for all plugins.
   - If a tag or date is given, only include commits since that point.
   - Default: all commits.

2. **Gather commit history**: Run `git log --oneline --name-only` with appropriate filters to get commits
   and their changed files.

3. **Map commits to plugins**: For each commit, identify which plugins were affected by checking which
   `plugins/<name>/` paths appear in the changed files.

4. **Read current versions**: Read each plugin's `plugin.json` to get the current version number.

5. **Build changelog structure**:
   ```markdown
   # Changelog

   ## plugin-name (v1.2.0)

   - feat: description of feature commit
   - fix: description of fix commit

   ## another-plugin (v1.0.1)

   - fix: description of fix
   ```

6. **Write or display**: Write the changelog to `CHANGELOG.md` in the project root. If one already exists,
   update it rather than overwriting — prepend new entries above existing content.

7. **Report**: Summarize how many plugins and commits were included.

## Important

- Use the conventional commit prefix (feat, fix, refactor, docs, chore) from the commit message if present.
- Skip commits that only touch non-plugin files (like root CLAUDE.md, CONTRIBUTING.md) unless they're
  relevant to a specific plugin.
- Collapse multiple commits for the same change into a single entry when appropriate.
