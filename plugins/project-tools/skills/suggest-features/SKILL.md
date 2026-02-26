---
name: suggest-features
description: Analyze the project and existing functionality to suggest new features worth adding
allowed-tools:

- Read
- Glob
- Grep
- Bash

---

Analyze the current project to suggest new features that would provide the most value, grounded in the codebase's
domain, patterns, and gaps.

## Steps

1. **Understand the project**: Read build/manifest files (`package.json`, `build.gradle.kts`, `Cargo.toml`,
   `pyproject.toml`, etc.) to identify the language, framework, and toolchain. Read the `README.md` if present to
   understand the project's stated purpose and scope.

2. **Explore the codebase**: Glob source and config files to map the project structure. Read key source files to
   understand the domain, existing features, data models, and API surface.

3. **Review recent activity**: Run `git log --oneline -50` to see recent commits and identify areas of active
   development, recurring patterns, or known gaps mentioned in commit messages.

4. **Identify feature gaps** by asking:
    - What common use cases in this domain are not yet supported?
    - What existing features are partially implemented or could be extended?
    - What quality-of-life improvements (better errors, configuration, defaults) are missing?
    - What integrations or interoperability improvements would be natural next steps?

5. **Produce suggestions**: Output a numbered list of suggested features. For each one include:
    - **Name**: a short descriptive title for the feature
    - **Description**: one or two sentences describing what it does and how it would work
    - **Rationale**: 1–2 sentences explaining why it would be valuable — reference concrete evidence from the codebase

6. **Rank by value**: Put the highest-impact suggestions first. A feature is high-impact if it addresses a clear gap,
   extends a heavily-used area, or is commonly expected in projects of this type.

## Important

- Suggestions must be grounded in the actual project — do not produce a generic list that would apply to any codebase.
- Do not suggest features that already exist, even partially.
- Do not make any code changes — only analyze and output suggestions.
- If the project is too small or new to have clear patterns, say so and offer a short list of high-value features for
  the detected stack instead.
