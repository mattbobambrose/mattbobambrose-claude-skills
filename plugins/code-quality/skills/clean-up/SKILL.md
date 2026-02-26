---
name: clean-up
description: Scan the codebase for cleanup opportunities and apply safe fixes
allowed-tools:

- Read
- Write
- Edit
- Glob
- Grep
- Bash

---

Scan the codebase for cleanup opportunities, apply safe fixes automatically, and present riskier changes for user
approval before acting.

## Steps

1. **Discover source and test files**: Glob all source files and test files. Read each one in full before making any
   changes.

2. **Scan for cleanup opportunities** across these categories:

   **Safe to auto-fix** (apply without asking):
    - Unused imports
    - Trailing whitespace and inconsistent blank lines
    - Commented-out code blocks (3+ consecutive lines of commented code)
    - `TODO`/`FIXME` comments that reference a resolved issue number (e.g., `// TODO #42 fix this` where #42 is closed)

   **Needs approval** (present to user before changing):
    - Dead code — functions, classes, or variables that are never referenced anywhere in the codebase
    - Duplicate logic — two or more code blocks that are identical or near-identical and could be extracted into a
      shared
      helper
    - Test cases whose names no longer match their assertions
    - Test files for source files that no longer exist

   **Surface only, do not touch** (report to user and stop):
    - `TODO`/`FIXME` comments that do not reference a resolved issue — list file, line, and the comment text so the
      user is aware
    - Magic numbers or strings that appear in multiple places — flag but don't refactor, as naming requires judgment

3. **Run the formatter** if one is configured for the project (e.g., `./gradlew ktfmtFormat`,
   `npx prettier --write .`, `gofmt -w .`, `cargo fmt`). Skip this step if no formatter is configured.

4. **Apply safe fixes**: Make all auto-fix changes directly. Keep each change minimal — do not reformat surrounding
   code that wasn't already being touched.

5. **Present approval-needed changes**: For each item in the "Needs approval" category, show:
    - The file and line number
    - A brief description of the issue
    - The proposed fix

   Ask the user to confirm which items to apply (e.g., "Apply items 1, 3? All? Skip all?"). Apply only the approved
   items.

6. **Verify**: Run the test suite (e.g., `./gradlew test`, `npm test`, `go test ./...`, `cargo test`) to confirm
   nothing was broken by the changes. If tests fail, revert the change that caused the failure and report it.

7. **Report**: Summarize:
    - What was auto-fixed (with file and line references)
    - What was approved and applied
    - What was skipped
    - Outstanding TODOs/FIXMEs that need attention
    - Test result (pass / fail / skipped)

## Important

- Read every file before editing anything — do not make changes based on file names alone.
- Never delete a function or class based solely on a grep showing no call sites — it may be called via reflection,
  serialization, or a framework. Flag it for the user instead.
- Never remove a `TODO` without either fixing the underlying issue or confirming with the user.
- Make one logical change at a time; do not bundle unrelated edits into a single file write.
- If the test suite was already failing before cleanup began, note this and do not treat new failures as caused by
  cleanup unless you can confirm they are.
