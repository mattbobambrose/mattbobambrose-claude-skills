---
name: draft-commit
description: Draft a commit message, show changed files, and optionally commit
argument-hint: "[file-path]"
allowed-tools:

- Bash
- Read

---

Suggest a git commit message for the current staged and unstaged changes, then commit if the user approves.

## Steps

1. **List changed files**: Run `git status --short` to get all modified, added, and deleted files. Display the list to
   the user as a numbered table with columns: status (M/A/D/R), file path.

2. **Check for an argument**: If `$ARGUMENTS` is a file path from the changed files list, show a side-by-side diff for
   that file using `git diff --word-diff=color` (or `git diff --cached --word-diff=color` if the file is staged). Then
   stop and wait for further input.

3. **Generate the diff**: Run `git diff` and `git diff --cached` to capture all unstaged and staged changes.

4. **Suggest a commit message**: Analyze the diff and draft a concise commit message:
    - First line: imperative mood summary under 70 characters (e.g., "Add domain methods for compiler and product
      feedback models")
    - If the change is non-trivial, add a blank line followed by a short body (1-3 bullet points) explaining the "why"
      not the "what"
    - The message must end with a blank line and: `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`

5. **Present to the user**: Show the suggested commit message and ask the user if they want to:
    - **Commit** — stage all changes and commit with the suggested message
    - **Edit** — let the user provide a revised message, then commit with that
    - **Cancel** — do nothing

6. **Commit**: If the user approves:
    - Stage changes with `git add` for the relevant files (prefer specific files over `git add -A`)
    - Commit using a HEREDOC for the message
    - Run `git status` to confirm success and show the result

## Important

- Never force-push or push to a remote — only create a local commit.
- Never use `--no-verify` or skip pre-commit hooks.
- Never amend an existing commit unless the user explicitly asks.
- If there are no changes to commit, inform the user and stop.
- Do not commit files that likely contain secrets (`.env`, credentials, tokens).
