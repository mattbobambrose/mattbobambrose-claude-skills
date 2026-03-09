---
name: git-check
description: Report uncommitted changes and unpushed commits in the current repo
allowed-tools:
  - Bash
---

Check the current git repository for uncommitted changes and unpushed commits.

## Steps

1. **Check for uncommitted changes**: Run `git status --porcelain` to detect staged, unstaged, and untracked
   files. Group results by status (staged, modified, untracked).

2. **Check for unpushed commits**: Run `git log @{upstream}..HEAD --oneline` to list commits that exist
   locally but have not been pushed. If there is no upstream tracking branch, note that the branch has no
   remote tracking configured.

3. **Report findings**:
   - If there are uncommitted changes, list them grouped by status with file counts
   - If there are unpushed commits, list each commit (hash + message)
   - If everything is clean and pushed, report that the repo is up to date

## Output Format

```
## Git Status

### Uncommitted Changes
- Staged: N file(s)
- Modified: N file(s)
- Untracked: N file(s)

### Unpushed Commits
- abc1234 commit message here
- def5678 another commit message

### Summary
N uncommitted change(s), M unpushed commit(s)
```

If clean: `Everything is committed and pushed.`

## Important

- This skill is read-only — it never commits, pushes, or modifies any files.
- If `git log @{upstream}..HEAD` fails (no upstream), report that instead of erroring.
