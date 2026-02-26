---
name: suggest-plugins
description: Analyze the project and existing plugins to suggest new plugins worth adding
allowed-tools:

- Read
- Glob
- Grep
- Bash

---

Analyze the current project and the existing skill library to suggest new skills that would provide the most value.

## Steps

1. **Inventory existing skills**: Glob `~/.claude/skills/**/SKILL.md` and read each one. Build a list of what is
   already covered so suggestions don't duplicate existing skills.

2. **Understand the project**:
    - Read `build.gradle.kts`, `settings.gradle.kts`, `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, or
      whatever build/manifest files exist to identify the language, framework, and toolchain.
    - Glob source, test, and config files to understand the project's structure and technology stack.
    - Read key source files to understand the domain, patterns, and recurring code shapes.

3. **Mine git history for repeated work**:
    - Run `git log --oneline -50` to see recent commit messages.
    - Look for patterns that suggest repetitive manual tasks (e.g., frequent "add X endpoint", "update Y config",
      "bump version", "scaffold Z").

4. **Identify gaps** by asking:
    - What repetitive tasks does this project's stack commonly involve that no existing skill covers?
    - What project-specific patterns (naming conventions, scaffolding, boilerplate) recur in the source code?
    - What workflow steps (build, release, deploy, migrate) are done manually and could be automated?
    - What quality checks (linting, formatting, dependency audits) are not yet covered?

5. **Produce suggestions**: Output a numbered list of suggested skills. For each one include:
    - **Name**: the proposed slash-command name (kebab-case, e.g., `add-migration`)
    - **Description**: one sentence describing what it does
    - **Invocation**: example invocation with any arguments
    - **Rationale**: 1–2 sentences explaining why it would be valuable for this specific project — reference concrete
      evidence from the codebase or git history

6. **Rank by value**: Put the highest-impact suggestions first. A skill is high-impact if it automates something
   done frequently, is error-prone when done manually, or requires remembering non-obvious project conventions.

## Important

- Do not suggest skills that substantially overlap with existing ones. If a gap could be filled by extending an
  existing skill, say so instead.
- Suggestions must be grounded in the actual project — do not produce a generic list that would apply to any codebase.
- If the project is too small or new to have clear patterns, say so and offer a short list of generally high-value
  skills for the detected stack instead.
- Do not create any files — only analyze and output suggestions.
