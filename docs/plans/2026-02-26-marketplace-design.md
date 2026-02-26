# Claude Code Community Marketplace

## Overview

A Git-based Claude Code marketplace repository. Users add it with `/plugin marketplace add mattbobambrose/mattbobambrose-claude-skills` and install plugins via the standard `/plugin` interface.

## Content

18 existing standalone skills grouped into 7 plugins by category:

| Plugin | Skills | Description |
|--------|--------|-------------|
| code-quality | find-bugs, fix-bugs, cleanup, check-naming, check-secrets | Code review, bug finding, and cleanup tools |
| documentation | add-docs, update-docs, update-readme | Documentation generation and maintenance |
| testing | suggest-tests, add-tests | Test discovery and coverage improvement |
| project-tools | suggest-features, suggest-plugins, draft-commit, create-skill | Project development and skill authoring |
| kotlin-tools | add-dto, add-dependency | Kotlin/Gradle-specific utilities |
| hello-world | hello-world | Simple hello world generator |
| summarize-progress | summarize-progress | Git history summarization |

## Repository Structure

```
mattbobambrose-claude-skills/
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   ├── code-quality/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/{find-bugs,fix-bugs,cleanup,check-naming,check-secrets}/SKILL.md
│   ├── documentation/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/{add-docs,update-docs,update-readme}/SKILL.md
│   ├── testing/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/{suggest-tests,add-tests}/SKILL.md
│   ├── project-tools/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/{suggest-features,suggest-plugins,draft-commit,create-skill}/SKILL.md
│   ├── kotlin-tools/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/{add-dto,add-dependency}/SKILL.md
│   ├── hello-world/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/hello-world/SKILL.md
│   └── summarize-progress/
│       ├── .claude-plugin/plugin.json
│       └── skills/summarize-progress/SKILL.md
├── CONTRIBUTING.md
├── README.md
└── .gitignore
```

## Key Files

- `.claude-plugin/marketplace.json` — marketplace catalog with all 7 plugins listed
- Each `plugin.json` — name, version (1.0.0), description, author, license
- SKILL.md files — ported from ~/git/claude-skills/ with original frontmatter and content
- `CONTRIBUTING.md` — guide for community plugin submissions
- `README.md` — browsable catalog of all plugins

## Community

- Open to PRs from anyone
- CONTRIBUTING.md provides step-by-step plugin creation guide
- PR template with checklist for valid structure
