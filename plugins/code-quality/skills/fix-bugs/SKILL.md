---
name: fix-bugs
description: Fix specified bugs from bugs-summary.md and add tests to verify fixes
argument-hint: "<bug-numbers>"
allowed-tools:

- Read
- Write
- Edit
- Glob
- Grep
- Bash

---

Fix the bugs specified by number in `$ARGUMENTS` and update `bugs-summary.md` to reflect the fixes.

## Argument format

`$ARGUMENTS` is a space-separated list of bug numbers from `bugs-summary.md`. Examples:

- `/fix-bugs 1 2 3` fixes bugs 1, 2, and 3
- `/fix-bugs 4` fixes bug 4 only

If `$ARGUMENTS` is empty, ask the user which bugs to fix.

## Steps

1. **Read `bugs-summary.md`**: Read the file from the project root. If it does not exist, tell the user to run
   `/find-bugs` first and stop.

2. **Parse bug numbers**: Extract the requested bug numbers from `$ARGUMENTS`. If any number does not correspond to an
   **Open** bug in the file, warn the user and skip that number. If a requested bug is already marked as **FIXED**, tell
   the user and skip it.

3. **For each bug to fix** (in numeric order):

   a. **Read the relevant source file(s)** referenced in the bug description.

   b. **Read existing test files** for the affected code to understand the current test coverage and patterns.

   c. **Implement the fix**: Make the minimum change needed to resolve the bug. Follow project conventions:
   - Match the existing code style (indentation, line length, naming patterns)
   - Do not introduce new patterns or styles that differ from surrounding code

   d. **Add or update tests** to verify the fix:
   - Add tests to existing test files when they cover the same class or module
   - Create new test files only when no existing test file covers the affected code
   - Follow the project's existing test conventions (framework, naming style, directory layout)
   - Each fix should have at least one test that would have failed before the fix and passes after

   e. **Run the test suite** using the project's test command to confirm the fix doesn't break anything and the new
   tests pass. If tests fail, investigate and fix the issue before moving on.

4. **Update `bugs-summary.md`**: Rewrite the file with the following structure:

    ```markdown
    # Bug Summary

    ## Fixed

    ### N. Short title (FIXED)

    **File:** `path/to/file.ext`

    Original description of the bug.

    **Fix:** Description of what was changed and what tests were added.

    ## Open

    ### M. Short title

    (remaining unfixed bugs)

    ## Design Observations (not bugs, but worth noting)

    (unchanged)
    ```

    - Move each fixed bug from **Open** to **Fixed** and append `(FIXED)` to its title
    - Add a **Fix:** paragraph describing the change and any new tests
    - Keep bug numbers stable (do not renumber)
    - Preserve unfixed bugs and design observations exactly as they were

5. **Run tests one final time** to confirm everything is green.

6. **Report**: Summarize what was fixed, what tests were added, and confirm all tests pass.

## Important

- Only fix the bugs listed in `$ARGUMENTS` — do not fix other bugs or make unrelated improvements.
- Make minimal, focused changes. Do not refactor surrounding code.
- Every fix must have a corresponding test. If a fix cannot be meaningfully tested, explain why.
- Never skip or disable existing tests to make a fix work.
- If a bug cannot be fixed without a design decision, describe the options and ask the user before proceeding.
