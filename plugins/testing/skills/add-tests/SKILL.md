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

1. **Detect the project stack**: Read build/manifest files to identify the language, test framework, and directory
   conventions.

2. **Discover source files**: Glob for production source files using patterns appropriate to the detected stack.

3. **Discover existing tests**: Glob for test files using the project's test directory conventions.

4. **Read every source and test file** to build a complete picture of what is and isn't covered.

5. **Identify coverage gaps** by checking each source file for:
    - Public functions/methods with no corresponding test
    - Edge cases not covered (empty inputs, boundary values, null/error conditions)
    - Serialization round-trip tests missing for data transfer objects
    - Classes or modules where individual methods lack dedicated unit tests

6. **Write the tests**:
    - Place tests alongside existing tests following the project's directory layout
    - Add tests to existing test files when they cover the same class or module; create new test files only when no
      existing test file covers the affected code
    - Follow the project's existing test conventions exactly — match the test framework, naming style, assertion
      library, indentation, and file organization found in existing tests
    - Use the same test patterns and helpers found in existing test files

7. **Build and verify**: Run the project's test command to confirm all new tests compile and pass.

8. **Report**: List every test added, grouped by file, with a one-line description of what each test verifies.

## Important

- Prioritize tests that catch real bugs (edge cases, boundary conditions, incorrect wiring) over trivial duplication of
  existing happy-path tests.
- Never duplicate a test that already exists — read existing tests carefully first.
- If a class has no dedicated test file, create one following the patterns in existing test files.
- Keep each test focused on a single behavior.
- Use realistic test data drawn from the project's own fixtures, samples, or documentation when available.
