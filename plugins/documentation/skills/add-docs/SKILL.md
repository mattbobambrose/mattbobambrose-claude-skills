---
name: add-docs
description: Add inline doc comments to source files that are missing them
argument-hint: "[directory-or-file]"
allowed-tools:

- Read
- Edit
- Glob
- Grep

---

Add inline documentation comments to public symbols that are currently undocumented.

## Scope

- If `$ARGUMENTS` is provided, restrict to source files under that directory or to that specific file.
- If `$ARGUMENTS` is empty, the scope is the entire repository — but **do not make any edits** until the user
  explicitly confirms (see step 3).

## Steps

1. **Detect the doc comment style** for the project's language:
    - Kotlin / Java → KDoc (`/** ... */`) with `@param`, `@return`, `@throws`
    - TypeScript / JavaScript → JSDoc (`/** ... */`) with `@param`, `@returns`, `@throws`
    - Python → docstrings (`"""..."""`), Google style
    - Go → `//` line comments directly above the declaration
    - Rust → `///` line comments
    - Other languages → use the idiomatic doc comment format for that language

2. **Discover and read source files** in the target scope:
    - Glob all source files under `$ARGUMENTS` (or the whole repo if no argument).
    - Read each file in full before writing anything.
    - Identify every public symbol (function, method, class, interface, property, constant) that has no doc comment or
      has only a placeholder comment (e.g., `// TODO`, empty `/** */`).

3. **Confirm scope if no argument was given**:
    - List every file that contains at least one undocumented public symbol, with a count of missing doc comments per
      file.
    - Ask the user: "Add doc comments to all N files listed above?" and wait for confirmation before proceeding.
    - If the user says no or asks to narrow the scope, stop and let them re-invoke with a specific directory or file.

4. **Write doc comments** for each undocumented symbol:
    - **Class / interface**: one sentence on its responsibility; note key invariants or constraints if non-obvious.
    - **Function / method**: what it does (not how), each parameter, the return value, and any exceptions/errors it can
      produce.
    - **Property / constant**: what the value represents and any valid range or format constraints.
    - Keep comments concise — prefer one clear sentence over a verbose paragraph.
    - Do not restate the symbol name or its type; say something the reader cannot already see from the signature.

5. **Preserve existing comments**: If a symbol already has a doc comment, skip it entirely — do not modify or extend it.

6. **Report**: List every file edited and the number of doc comments added. Flag any symbols whose purpose was too
   unclear to document accurately — the user will need to fill those in.

## Important

- Never guess at behavior. If a function's purpose cannot be inferred from its name, parameters, return type, and
  implementation, write `// TODO: document this` and flag it in the report rather than inventing a description.
- Do not add doc comments to private, internal, or package-private symbols unless `$ARGUMENTS` targets a specific file
  and the user has clearly asked for full coverage.
- Do not reformat or otherwise touch lines outside the doc comment being added.
- Do not add a doc comment that says only what the signature already says (e.g., `` /** Gets the name. */ `` above
  `fun getName()`).
