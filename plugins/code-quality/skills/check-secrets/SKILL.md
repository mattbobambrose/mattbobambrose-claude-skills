---
name: check-secrets
description: Check git history and working tree for committed secrets files or .env files, and ensure they are covered
by .gitignore
allowed-tools:

- Bash
- Read
- Write
- Edit
- Glob

---

Audit the repository for secrets files, `.env` files, and other sensitive files that should never be committed. Check
both the current working tree and git history, then ensure every sensitive file pattern is covered by `.gitignore`.

## Steps

1. **Scan the working tree for sensitive files**: Glob for common secrets file patterns across the entire project:
    - `.env`, `.env.*`, `*.env`
    - `secrets.*`, `*secret*`, `*credentials*`, `*password*`
    - `*.pem`, `*.key`, `*.p12`, `*.pfx`, `*.jks`
    - `id_rsa`, `id_ed25519`, and other common private key names
    - `config/secrets.*`, `config/credentials.*`
    - Any file named `secrets`, `credentials`, or `token` (with or without extension)

2. **Check git tracking**: For each sensitive file found, run `git ls-files <file>` to determine if it is currently
   tracked by git. Flag any tracked files as a critical finding.

3. **Scan git history**: Run `git log --all --full-history -- '*.env' '*.pem' '*.key' '*secret*' '*credential*'` (and
   similar patterns) to detect if any secrets files were ever committed, even if later deleted. Report any historical
   matches.

4. **Read `.gitignore`**: Read the root `.gitignore` (and any nested `.gitignore` files) to understand what is currently
   excluded. Identify any sensitive file patterns found in steps 1–3 that are not covered.

5. **Update `.gitignore`**: For any sensitive patterns not already covered, append them to the root `.gitignore` with a
   comment explaining why they are excluded. Do not duplicate entries that are already present.

6. **Report findings**: Summarize:
    - **Currently tracked secrets** (critical — requires manual `git rm --cached` and a history rewrite)
    - **Historical secrets in git history** (high — recommend `git filter-repo` or BFG to scrub)
    - **Untracked sensitive files now added to `.gitignore`** (resolved)
    - **No issues found** if the repo is clean

7. **Offer to install a pre-commit hook**: Ask the user if they would like a `pre-commit` hook installed to
   automatically
   block commits that add sensitive files in the future. If they agree:
    - Check if `.git/hooks/pre-commit` already exists. If it does, read it and append to it rather than overwriting.
    - Write (or append) a hook script that:
        - Scans staged files (`git diff --cached --name-only`) against the same sensitive patterns used in step 1
        - Exits with a non-zero status and a clear error message if any match is found, blocking the commit
        - Is POSIX-compatible (`#!/bin/sh`) so it works without any external dependencies
    - Make the hook executable with `chmod +x .git/hooks/pre-commit`.
    - Remind the user that `.git/hooks/` is not committed to the repo, so teammates will need to install it separately —
      suggest checking in a `scripts/install-hooks.sh` or using a tool like [pre-commit](https://pre-commit.com) if
      team-wide enforcement is needed.

## Important

- Do not print or log the contents of any secrets files — only report file paths and patterns.
- Do not remove or modify any secrets files themselves — only update `.gitignore`.
- If currently tracked secrets are found, clearly warn the user that removing a file from git tracking requires
  `git rm --cached` and that the secret may need to be rotated if the repo has ever been pushed to a remote.
- Treat historical git exposure as a serious finding even if the file no longer exists in the working tree.
