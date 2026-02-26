---
name: check-naming
description: Check variable names in project source files for appropriateness and suggest improvements
argument-hint: "[file|package|project]"
allowed-tools:

- Read
- Glob
- Grep

---

Scan source files for poorly named variables, functions, parameters, and other identifiers, then suggest clearer
alternatives. The user can optionally specify a scope — a single file path, a package/directory path, or `project`
(the default) to check everything.

## Steps

1. **Determine scope**: Parse the optional argument to decide what to scan.

    - If a file path is given (e.g., `src/main/kotlin/Foo.kt`), scan only that file.
    - If a directory or package path is given (e.g., `src/main/kotlin/com/example/api`), scan all source files under
      that directory.
    - If `project` is given or no argument is provided, scan all source files in the project.

2. **Discover source files**: Based on the scope, glob for source files. Use language-appropriate patterns for the
   project (e.g., `**/*.kt`, `**/*.java`, `**/*.ts`, `**/*.py`, `**/*.go`). Exclude generated code, build output,
   and `node_modules`.

3. **Read every file** in the scope to understand the full context before making judgments. Variable names that seem
   unclear in isolation may make perfect sense within their surrounding code.

4. **Evaluate identifier names** by checking for these issues:

   **Single-letter or overly short names** (outside of conventional uses like `i`/`j` for loop indices, `e` for
   exceptions, `x`/`y` for coordinates):
    - Variables like `a`, `b`, `s`, `t`, `m` that carry no meaning
    - Abbreviations that aren't universally understood (e.g., `mgr`, `ctx`, `val`, `btn` — unless idiomatic for
      the language/framework)

   **Overly long or verbose names**:
    - Names that could be shortened without losing meaning (e.g., `numberOfItemsInTheShoppingCart` vs `cartItemCount`)
    - Redundant prefixes or suffixes (e.g., `userDataInfo`, `stringValue`)

   **Misleading or inaccurate names**:
    - Boolean variables that don't read as questions (e.g., `status` vs `isActive`)
    - Functions whose names don't reflect what they actually do
    - Variables whose type contradicts their name (e.g., a `List` named `item` instead of `items`)
    - Names that suggest a different type or behavior than what the code implements

   **Inconsistent conventions**:
    - Mixed naming styles within the same file or module (e.g., `camelCase` mixed with `snake_case` in a language
      where one is standard)
    - Similar concepts named differently across files (e.g., `user` in one file and `account` in another for the
      same entity)

   **Generic or meaningless names**:
    - `data`, `result`, `value`, `item`, `temp`, `obj`, `thing`, `stuff`, `info`, `tmp` when a more descriptive
      name is possible
    - `handle`, `process`, `manage`, `do` as function prefixes that don't convey specific behavior

5. **Compile findings**: For each issue found, record:
    - The file path and line number
    - The current identifier name
    - Why it is problematic
    - One or two suggested replacements

6. **Report**: Present the findings grouped by file. For each file, list the identifiers with issues in a table:

   ```
   ### path/to/file.kt

   | Line | Current Name | Issue              | Suggested Name(s)        |
   |------|--------------|--------------------|--------------------------|
   | 12   | `s`          | Too short          | `searchQuery`, `input`   |
   | 34   | `processIt`  | Vague function name| `validateOrder`          |
   ```

   End with a summary line: how many files scanned, how many identifiers flagged, and a breakdown by issue category.

## Important

- Do not make any code changes — only analyze and report findings.
- Read files fully before judging names; context matters. A variable named `n` in a well-known algorithm
  implementation may be perfectly appropriate.
- Respect language idioms. For example, `_` for unused variables in Go/Kotlin, `self`/`cls` in Python, and `$` prefixes
  in shell scripts are all conventional and should not be flagged.
- Do not flag names in generated code, vendored dependencies, or test fixtures where descriptive naming adds no value.
- Do not flag names that follow a library or framework's established conventions (e.g., `ctx` in Go's `context.Context`
  or `req`/`res` in Express handlers).
- When the scope is large, prioritize production source code over test files.
