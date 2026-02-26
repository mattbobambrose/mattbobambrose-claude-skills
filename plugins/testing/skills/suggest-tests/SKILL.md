---
name: suggest-tests
description: Suggest new tests to write based on existing source code and test coverage
allowed-tools:

- Read
- Glob
- Grep

---

Analyze the codebase and suggest new tests to write.

## Steps

1. **Detect the project stack**: Read build/manifest files to identify the language, test framework, and directory
   conventions.

2. **Discover source files**: Glob for production source files using patterns appropriate to the detected stack.

3. **Discover existing tests**: Glob for test files using the project's test directory conventions.

4. **Read each source and test file** to understand what is already covered.

5. **Analyze gaps**: For each source file, identify:
    - Public functions/methods with no corresponding test
    - Edge cases not covered by existing tests (e.g., empty inputs, boundary values, error conditions)
    - Serialization round-trip tests missing for data transfer objects
    - Integration points between modules that lack end-to-end verification

6. **Output a numbered list** of suggested tests, grouped by source file. For each suggestion include:
    - The source file and function/class being tested
    - A descriptive test name following the project's naming conventions
    - A brief explanation of what the test should verify and why it matters

## Important

- Do NOT write any code — only suggest tests.
- Prioritize tests that catch real bugs over trivial happy-path tests that already exist.
- Follow the project's existing test conventions (framework, naming style, assertion patterns).
- If test coverage looks comprehensive, say so and suggest only high-value additions.
