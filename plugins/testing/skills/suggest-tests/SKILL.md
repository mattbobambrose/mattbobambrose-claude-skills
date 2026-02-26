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

1. **Read all source files**: Glob `src/main/kotlin/**/*.kt` to find all production code.

2. **Read all existing tests**: Glob `src/test/kotlin/**/*.kt` to find all existing test files.

3. **Read each source and test file** to understand what is already covered.

4. **Analyze gaps**: For each source file, identify:
    - Public functions/methods with no corresponding test
    - Edge cases not covered by existing tests (e.g., empty inputs, boundary values, error conditions)
    - Serialization round-trip tests missing for `@Serializable` data classes
    - DTOs without deserialization tests against sample JSON in `docs/` or `src/test/resources/`

5. **Output a numbered list** of suggested tests, grouped by source file. For each suggestion include:
    - The source file and function/class being tested
    - A descriptive backtick-enclosed test name (e.g., `` `should return empty list when no items match` ``)
    - A brief explanation of what the test should verify and why it matters

## Important

- Do NOT write any code — only suggest tests.
- Prioritize tests that catch real bugs over trivial happy-path tests that already exist.
- Follow project conventions: Kotest `AnnotationSpec`, `@Test`, backtick-enclosed descriptive names.
- If test coverage looks comprehensive, say so and suggest only high-value additions.
