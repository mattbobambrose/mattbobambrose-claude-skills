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

1. **Detect the project stack**: Read build/manifest files (e.g., `package.json`, `build.gradle.kts`, `Cargo.toml`,
   `go.mod`, `pyproject.toml`, `pom.xml`) to identify the language, framework, and build tool.

2. **Discover source files**: Glob for production source files using patterns appropriate to the detected stack (e.g.,
   `src/**/*.ts`, `**/src/main/**/*.kt`, `**/*.go`, `src/**/*.py`).

3. **Discover test files**: Glob for test files using the project's test directory conventions.

4. **Read build configuration** to understand dependencies, plugins, and build setup.

5. **Read every source file** to understand the full codebase before looking for issues.

6. **Read every test file** to understand what is already tested and whether tests are correct.

7. **Run the test suite** using the project's test command (e.g., `npm test`, `./gradlew test`, `cargo test`,
   `go test ./...`, `pytest`) to check for failing tests.

8. **Identify bugs** by looking for:
    - Logic errors (incorrect conditionals, off-by-one, wrong operator)
    - Missing input validation or error handling
    - Mismatched test names vs assertions
    - Visibility issues (public API surface leaking internals)
    - Resource leaks (unclosed clients, streams, connections)
    - Thread safety issues
    - Serialization/deserialization mismatches
    - Silent failures (operations that fail without warning)
    - Incorrect or missing null/error handling
    - Duplicated code that has diverged (copy-paste bugs)

9. **Check for design issues** worth noting:
    - Hardcoded values that should be configurable
    - Equality/comparison semantics that may surprise callers
    - Missing API contracts or invariants

10. **Write `bugs-summary.md`**: Create or overwrite `bugs-summary.md` in the project root with the following structure:

    ```markdown
    # Bug Summary

    ## Open

    ### 1. Short title

    **File:** `path/to/file.ext`

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

11. **Report**: Summarize how many bugs were found and give a brief overview of the findings.

## Important

- Read all source files before reporting bugs — do not guess based on file names alone.
- Every reported bug must reference a specific file and describe a concrete problem.
- Do not report stylistic preferences or nitpicks as bugs.
- Do not make any code changes — only analyze and write the summary file.
- Run the tests to verify the current state; note any failures as bugs.
