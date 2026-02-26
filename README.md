# mattbobambrose-claude-skills

Community marketplace for Claude Code plugins — code quality, documentation, testing, and more.

## Installation

Add this marketplace to your Claude Code installation:

```
/plugin marketplace add mattbobambrose/mattbobambrose-claude-skills
```

To install an individual plugin:

```
/plugin install code-quality@mattbobambrose-claude-skills
```

## Updating Plugins

Update all plugins from this marketplace:

```
/plugin marketplace update mattbobambrose-claude-skills
```

Update a single plugin:

```
/plugin update code-quality@mattbobambrose-claude-skills
```

## Plugin Catalog

| Plugin | Description | Skills | Category |
|--------|-------------|--------|----------|
| `code-quality` | Code review, bug finding, cleanup, naming checks, and secrets auditing | 5 | development |
| `documentation` | Generate and maintain inline docs, API documentation, and READMEs | 3 | documentation |
| `testing` | Suggest new tests and raise test coverage | 2 | testing |
| `project-tools` | Suggest features, suggest skills, draft commits, and scaffold new skills | 4 | productivity |
| `kotlin-tools` | Kotlin/Gradle utilities — generate DTOs from JSON and add Maven dependencies | 2 | development |
| `hello-world` | Generate a hello world program in any language | 1 | development |
| `summarize-progress` | Summarize accomplishments from git history with motivational messaging | 1 | productivity |

## Plugins

### code-quality

Code review, bug finding, cleanup, naming checks, and secrets auditing.

```
/plugin install code-quality@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/find-bugs` | Review the codebase for bugs and write findings to bugs-summary.md |
| `/fix-bugs <bug-numbers>` | Fix specified bugs from bugs-summary.md and add tests to verify fixes |
| `/clean-up` | Scan the codebase for cleanup opportunities and apply safe fixes |
| `/check-naming [file\|package\|project]` | Check variable names in project source files for appropriateness and suggest improvements |
| `/check-secrets` | Check git history and working tree for committed secrets files or .env files, and ensure they are covered by .gitignore |

### documentation

Generate and maintain inline docs, API documentation, and READMEs.

```
/plugin install documentation@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/add-docs [directory-or-file]` | Add inline doc comments to source files that are missing them |
| `/update-docs [output-dir]` | Create or update Markdown documentation for the project's public API and modules |
| `/update-readme` | Analyze the codebase and update the README with accurate, up-to-date content |

### testing

Suggest new tests and raise test coverage.

```
/plugin install testing@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/suggest-tests` | Suggest new tests to write based on existing source code and test coverage |
| `/raise-coverage` | Add additional tests to raise test coverage |

### project-tools

Suggest features, suggest skills, draft commits, and scaffold new skills.

```
/plugin install project-tools@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/suggest-features` | Analyze the project and existing functionality to suggest new features worth adding |
| `/suggest-skills` | Analyze the project and existing skills to suggest new skills worth adding |
| `/suggest-commit [file-path]` | Suggest a commit message, show changed files, and optionally commit |
| `/create-skill <skill-name> [description]` | Scaffold a new skill with correct frontmatter and structure, and add it to the README |

### kotlin-tools

Kotlin/Gradle utilities — generate DTOs from JSON and add Maven dependencies.

```
/plugin install kotlin-tools@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/add-dto [FileName.kt] [json-string]` | Create a Kotlin serializable DTO data class from a JSON string |
| `/add-dependency <group:artifact> [group:artifact ...]` | Add a Maven dependency to the Kotlin project's Gradle version catalog and build file |

### hello-world

Generate a hello world program in any language.

```
/plugin install hello-world@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/hello-world [language]` | Generate a hello world program in a specified programming language |

### summarize-progress

Summarize accomplishments from git history with motivational messaging.

```
/plugin install summarize-progress@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/summarize-progress [today \| yesterday \| last week \| last month \| date]` | Summarize what the user accomplished over a time period based on git history and provide motivational messaging |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on adding new plugins and skills to this marketplace.
