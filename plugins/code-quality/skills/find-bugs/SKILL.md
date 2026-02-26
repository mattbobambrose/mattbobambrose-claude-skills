---
name: find-bugs
description: Review the codebase for bugs and write findings to bugs-summary.md
allowed-tools:

- Read
- Write
- Glob
- Grep
- Bash

---

Review the entire codebase for bugs and write a summary to `bugs-summary.md` in the project root.

## Steps

1. **Discover source files**: Glob `**/src/main/kotlin/**/*.kt` across all subprojects to find all production code.

2. **Discover test files**: Glob `**/src/test/kotlin/**/*.kt` across all subprojects to find all test files.

3. **Read build configuration**: Read `build.gradle.kts`, `settings.gradle.kts`, `gradle/libs.versions.toml`, and each
   subproject's `build.gradle.kts` to understand dependencies and build setup.

4. **Read every source file** to understand the full codebase before looking for issues.

5. **Read every test file** to understand what is already tested and whether tests are correct.

6. **Run the test suite**: Execute `./gradlew test` to check for failing tests.

7. **Identify bugs** by looking for:
    - Logic errors (incorrect conditionals, off-by-one, wrong operator)
    - Missing input validation or error handling
    - Mismatched test names vs assertions
    - Visibility issues (public API surface leaking internals)
    - Resource leaks (unclosed clients, streams, connections)
    - Thread safety issues
    - Serialization/deserialization mismatches between DTOs and sample JSON
    - Silent failures (operations that fail without warning)
    - Incorrect or missing null handling
    - Duplicated code that has diverged (copy-paste bugs)

8. **Check for design issues** worth noting:
    - Hardcoded values that should be configurable
    - Data class equality semantics that may surprise callers
    - Missing API contracts or invariants

9. **Write `bugs-summary.md`**: Create or overwrite `bugs-summary.md` in the project root with the following structure:

    ```markdown
    # Bug Summary

    ## Open

    ### 1. Short title

    **File:** `path/to/file.kt`

    Description of the bug, why it's a problem, and what the expected behavior should be.

    ### 2. ...

    ## Design Observations (not bugs, but worth noting)

    ### Short title

    Description.
    ```

    - Number each bug sequentially starting at 1
    - Include the file path and relevant line numbers
    - Explain both the problem and its impact
    - Separate true bugs from design observations
    - If a previous `bugs-summary.md` exists, read it first and preserve any items marked as "FIXED" in a
      **Fixed** section at the top

10. **Report**: Summarize how many bugs were found and give a brief overview of the findings.

## Important

- Read all source files before reporting bugs — do not guess based on file names alone.
- Every reported bug must reference a specific file and describe a concrete problem.
- Do not report stylistic preferences or nitpicks as bugs.
- Do not make any code changes — only analyze and write the summary file.
- Run the tests to verify the current state; note any failures as bugs.
