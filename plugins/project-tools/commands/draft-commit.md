---
name: draft-commit
description: Draft a commit message, show changed files, and optionally commit
argument-hint: "[file-path]"
allowed-tools:
  - Bash
  - Read
---

Suggest a git commit message for the current staged and unstaged changes, then commit if the user approves.

If `$ARGUMENTS` is a file path, show a word-diff for that file (`git diff --word-diff=color` or `git diff --cached --word-diff=color` if staged) and stop.

Otherwise:
1. Run `git status --short` and display changed files as a numbered table (status, path)
2. Run `git diff` and `git diff --cached` to capture all changes
3. Draft a commit message: imperative mood summary under 70 chars, optional 1-3 bullet body explaining "why" not "what", ending with `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`
4. Ask the user: **Commit**, **Edit**, or **Cancel**
5. If approved, stage relevant files (prefer specific files over `git add -A`), commit via HEREDOC, run `git status`

Never force-push, push to remote, use `--no-verify`, or amend unless asked. Do not commit secrets.
