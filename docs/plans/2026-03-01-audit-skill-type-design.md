# Design: audit-skill-type Skill

**Date:** 2026-03-01
**Plugin:** project-tools
**Type:** New skill (pure analysis, read-only)

## Purpose

Scan all `SKILL.md` files in the `plugins/` directory and evaluate whether each is better suited as a **skill** (rich markdown instructions with workflows/steps/references) or a **command** (lightweight executable action). Output a recommendation table.

## Scope

- Analyzes only skills within this marketplace repo (`plugins/*/skills/*/SKILL.md`)
- Read-only — does not modify any files
- Follows the pattern of `suggest-features` and `suggest-plugins`

## Complexity-Based Criteria

Each skill is evaluated against these signals:

| Signal             | Favors Skill                                      | Favors Command                          |
|--------------------|---------------------------------------------------|-----------------------------------------|
| Step count         | 3+ steps with branching logic                     | 1-2 simple linear steps                 |
| Context needs      | Requires domain knowledge or codebase understanding | Needs only file paths or simple args   |
| Tool variety       | Uses 3+ different tools                           | Uses mainly just Bash or Write          |
| Output complexity  | Structured analysis, reports, multi-file changes   | Single file or simple output            |
| Bundled resources  | References scripts/, references/, assets/          | None needed                             |

### Scoring

- Each signal is evaluated as favoring skill or command
- Majority wins; ties default to skill (since skills are the more capable option)
- Confidence: **High** (4-5 signals agree), **Medium** (3 signals agree), **Low** (mixed signals)

## Output Format

A markdown table:

```
| # | Skill | Plugin | Recommendation | Confidence | Reasoning |
|---|-------|--------|---------------|------------|-----------|
| 1 | hello-world | hello-world | Command | High | Single step, one tool (Write), no analysis needed |
| 2 | find-bugs | code-quality | Skill | High | 11 steps, 5 tools, deep codebase analysis |
```

Followed by a summary: total skills analyzed, count of each recommendation, and any notable observations.

## Allowed Tools

- `Read` — to read SKILL.md files
- `Glob` — to discover SKILL.md files
- `Grep` — to count patterns (steps, tool references)

## Naming

- Skill name: `audit-skill-type`
- Description: "Analyze each skill in the marketplace and recommend whether it is better suited as a skill or command"

## Implementation Notes

- Parse YAML frontmatter to extract `name`, `allowed-tools` list
- Count `## Steps` numbered items for step count
- Check `allowed-tools` list length for tool variety
- Read the body to assess context needs and output complexity (requires judgment)
- No bundled resources needed — this is a minimal skill
