---
name: add-tests
description: Add additional tests to raise test coverage
allowed-tools:

- Read
- Write
- Edit
- Glob
- Grep
- Bash

---

Analyze the codebase for test coverage gaps and write new tests to fill them.

## Steps

1. **Discover source files**: Glob `**/src/main/kotlin/**/*.kt` across all subprojects to find all production code.

2. **Discover existing tests**: Glob `**/src/test/kotlin/**/*.kt` across all subprojects to find all test files.

3. **Read every source and test file** to build a complete picture of what is and isn't covered.

4. **Identify coverage gaps** by checking each source file for:
    - Public functions/methods with no corresponding test
    - Edge cases not covered (empty inputs, boundary values, nullability, error conditions)
    - Serialization round-trip tests missing for `@Serializable` data classes
    - DTOs without deserialization tests against sample JSON in `docs/`
    - Builder/DSL classes without tests for observation accumulation, correct model names, or correct observation names
    - Domain method classes where individual methods lack dedicated unit tests

5. **Write the tests**:
    - Place tests in the same subproject as the source file being tested (e.g., source in
      `json-bayesian-dsl/src/main/kotlin/` gets tests in `json-bayesian-dsl/src/test/kotlin/`)
    - Add tests to existing test files when they cover the same class; create new test files only for classes with no
      existing test file
    - Follow project conventions exactly:
        - Kotest `AnnotationSpec` with `@Test` annotation
        - Backtick-enclosed descriptive method names (e.g., `` `age should set correct observation name` ``)
        - 2-space indentation
        - Import `io.kotest.core.spec.style.AnnotationSpec`, `io.kotest.matchers.shouldBe`, and other matchers as needed
    - Use the same test patterns found in existing test files (e.g., `TestBuilder` inner classes for domain method
      tests)

6. **Build and verify**: Run `./gradlew clean build` to confirm all new tests compile and pass.

7. **Report**: List every test added, grouped by file, with a one-line description of what each test verifies.

## Important

- Prioritize tests that catch real bugs (edge cases, boundary conditions, incorrect wiring) over trivial duplication of
  existing happy-path tests.
- Never duplicate a test that already exists — read existing tests carefully first.
- If a domain method class (e.g., `InitialModelDomainMethods`) has no dedicated test file, create one following the
  pattern in `ProductFeedbackModelDomainMethodsTest.kt` or `CompilerModelDomainMethodsTest.kt`.
- Keep each test focused on a single behavior.
- Use values from `docs/bayesian-json/` sample JSON files when realistic test data is needed.
