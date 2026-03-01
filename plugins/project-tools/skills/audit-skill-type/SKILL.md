---
name: audit-skill-type
description: Analyze each skill in the marketplace and recommend whether it is better suited as a skill or command
allowed-tools:

- Read
- Glob
- Grep

---

Scan every `SKILL.md` in the `plugins/` directory and evaluate whether each is better implemented as a **skill**
(rich markdown instructions with multi-step workflows) or a **command** (lightweight executable action). Output a
recommendation table.

## Background

In the Claude Code plugin system, **skills** and **commands** serve different purposes:

- **Skills** provide rich context, multi-step workflows, domain knowledge, and references. They are loaded into the
  context window and guide Claude through complex analysis or generation tasks.
- **Commands** are lightweight, deterministic actions — typically a single shell command or short script. They execute
  quickly with minimal context.

Most items in this marketplace are skills. This audit identifies any that would be better served as commands.

## Criteria

Evaluate each skill against five complexity signals:

| Signal             | Favors Skill                                       | Favors Command                           |
|--------------------|----------------------------------------------------|-----------------------------------------|
| Step count         | 3+ steps with branching or conditional logic        | 1–2 simple linear steps                 |
| Context needs      | Requires domain knowledge or codebase understanding | Needs only file paths or simple args     |
| Tool variety       | Uses 3+ different tools from `allowed-tools`        | Uses mainly Bash or Write alone          |
| Output complexity  | Produces structured analysis, reports, multi-file changes | Produces a single file or simple output |
| Bundled resources  | References or would benefit from scripts/, references/, assets/ | None needed               |

### Scoring

- Count how many of the five signals favor skill vs command.
- **Majority wins**; ties default to **skill** (skills are the more capable option).
- **Confidence**: High (4–5 signals agree), Medium (3 signals agree), Low (mixed signals or edge case).

## Steps

1. **Discover all skills**: Glob for `plugins/*/skills/*/SKILL.md` to find every skill in the marketplace.

2. **Read each skill**: For each `SKILL.md`, read the full file. Parse:
    - The `name` and `allowed-tools` fields from the YAML frontmatter
    - The plugin name from the file path
    - The number of numbered steps under `## Steps`
    - The body content for context needs and output complexity assessment

3. **Evaluate each signal** for the skill:
    - **Step count**: Count numbered items under `## Steps`. 3+ with branching favors skill.
    - **Context needs**: Does the body reference reading multiple files, understanding codebases, analyzing domains,
      or applying judgment? If yes, favors skill.
    - **Tool variety**: Count tools in `allowed-tools`. 3+ favors skill; 1–2 or absent favors command.
    - **Output complexity**: Does the skill produce structured reports, tables, multi-file edits, or analysis? If yes,
      favors skill. Single-file output or simple text favors command.
    - **Bundled resources**: Does the skill reference or would it benefit from scripts/, references/, or assets/
      directories? If yes, favors skill.

4. **Determine recommendation**: Apply the scoring rules above to produce a recommendation (Skill or Command) and
   confidence level (High, Medium, Low) for each.

5. **Build the recommendation table**: Output a markdown table with these columns:

    | # | Skill | Plugin | Recommendation | Confidence | Reasoning |

    - Number rows sequentially starting at 1
    - Include all skills found in the marketplace
    - Reasoning should be one sentence citing the key signal(s) that drove the recommendation

6. **Summarize**: After the table, output:
    - Total skills analyzed
    - Count recommended to stay as skills vs convert to commands
    - Any notable observations (e.g., skills that are borderline, or patterns across plugins)

## Important

- Do not modify any files — this is a read-only analysis.
- Read every SKILL.md in full before evaluating — do not guess based on file names alone.
- Be honest about borderline cases; use Low confidence and explain the tension.
- If a skill is already well-suited to its current type, say so — not every audit should recommend changes.
