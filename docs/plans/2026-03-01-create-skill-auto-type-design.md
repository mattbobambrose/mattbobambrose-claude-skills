# Design: Auto-determining skill vs command in create-skill

## Summary

Modify the existing `create-skill` skill to automatically determine whether a new addition should be scaffolded as a **skill** (rich multi-step workflow) or a **command** (lightweight single action), based on the audit-skill-type criteria.

## Decisions

- **Name**: Keep `/create-skill` — familiar and already in use
- **Detection**: Auto-determine type from the user's description using the 5 audit criteria
- **Override**: Present the recommendation with reasoning, then let the user confirm or override
- **Approach**: Inline the criteria directly (self-contained, no cross-skill dependency)

## Criteria (inlined from audit-skill-type)

| Signal | Favors Skill | Favors Command |
|--------|-------------|----------------|
| Step count | 3+ steps with branching/conditional logic | 1-2 simple linear steps |
| Context needs | Requires domain knowledge or codebase understanding | Needs only file paths or simple args |
| Tool variety | Would use 3+ different tools | Would use mainly Bash or Write alone |
| Output complexity | Produces structured analysis, reports, multi-file changes | Produces a single file or simple output |
| Bundled resources | Benefits from scripts/, references/, assets/ | None needed |

Majority wins; ties default to skill.

## Flow

1. Parse name and description from arguments (same as today)
2. Evaluate the 5 criteria against the description
3. Present recommendation with per-signal justification
4. Ask user to confirm or override
5. Scaffold the appropriate type

## Scaffolding

### Skill (same as today)

- Path: `plugins/<plugin>/skills/<name>/SKILL.md`
- Structure: YAML frontmatter + paragraph + `## Steps` + `## Important`

### Command (new)

- Path: `plugins/<plugin>/commands/<name>.md`
- Structure: YAML frontmatter + concise body with bullet points
- No `## Steps` or `## Important` sections
- Mirrors the `hello-world` command format

## Files changed

1. `plugins/project-tools/skills/create-skill/SKILL.md` — main modification
2. `plugins/project-tools/.claude-plugin/plugin.json` — version bump (patch)
3. `.claude-plugin/marketplace.json` — version bump (patch)
