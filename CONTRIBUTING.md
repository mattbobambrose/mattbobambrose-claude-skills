# Contributing to mattbobambrose-claude-skills

Thank you for your interest in contributing plugins and skills to this marketplace. This guide covers how to create new plugins, add skills, and submit your changes.

## Repository Structure

```
mattbobambrose-claude-skills/
├── .claude-plugin/
│   └── marketplace.json          # Root marketplace catalog
├── plugins/
│   └── <plugin-name>/
│       ├── .claude-plugin/
│       │   └── plugin.json       # Plugin metadata
│       └── skills/
│           └── <skill-name>/
│               └── SKILL.md      # Skill definition
├── README.md
└── CONTRIBUTING.md
```

## Creating a New Plugin

### 1. Create the plugin directory

```bash
mkdir -p plugins/<plugin-name>/.claude-plugin
mkdir -p plugins/<plugin-name>/skills
```

Use kebab-case for the plugin name (e.g., `code-quality`, `kotlin-tools`).

### 2. Create plugin.json

Create `plugins/<plugin-name>/.claude-plugin/plugin.json`:

```json
{
  "name": "<plugin-name>",
  "version": "1.0.0",
  "description": "Short description of what the plugin provides",
  "author": {
    "name": "<your-github-username>"
  }
}
```

### 3. Add at least one skill

See "Adding a Skill to an Existing Plugin" below.

### 4. Register the plugin in marketplace.json

Add a new entry to the `plugins` array in `.claude-plugin/marketplace.json`:

```json
{
  "name": "<plugin-name>",
  "source": "./plugins/<plugin-name>",
  "description": "Short description of what the plugin provides",
  "version": "1.0.0",
  "author": { "name": "<your-github-username>" },
  "category": "<category>"
}
```

Valid categories: `development`, `documentation`, `testing`, `productivity`.

## Adding a Skill to an Existing Plugin

### 1. Create the skill directory

```bash
mkdir -p plugins/<plugin-name>/skills/<skill-name>
```

Use kebab-case for the skill name (e.g., `find-bugs`, `add-docs`).

### 2. Create SKILL.md

Create `plugins/<plugin-name>/skills/<skill-name>/SKILL.md` with the following structure:

```markdown
---
name: <skill-name>
description: One-sentence description of what the skill does
argument-hint: "[optional-arguments]"
allowed-tools:

- Bash
- Read
- Write
- Edit
- Glob
- Grep

---

One paragraph explaining what this skill does and when to use it.

## Steps

1. **Step one**: Description of the first step.

2. **Step two**: Description of the second step.

3. **Step three**: Description of the third step.

## Important

- Key constraint or safety rule.
- Another constraint.
```

#### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Skill name in kebab-case, used as the slash command |
| `description` | Yes | One-sentence description shown in help and catalogs |
| `argument-hint` | No | Hint shown after the slash command (e.g., `"<file-path>"`) |
| `allowed-tools` | No | List of Claude Code tools the skill is permitted to use |

#### Writing Guidelines

- **Steps** should be numbered and use bold for the step title.
- **Important** section lists constraints, safety rules, and things the skill must never do.
- Additional sections (e.g., `## Argument format`, `## Scope`, `## Formatting rules`) are fine when a skill needs them — `## Steps` is required, other sections are at your discretion.
- Keep descriptions concise and action-oriented.
- Only include tools in `allowed-tools` that the skill actually needs.
- Remove the `argument-hint` field entirely if the skill takes no arguments.
- Always quote `argument-hint` values in the frontmatter (e.g., `argument-hint: "[file-path]"`).

## Updating marketplace.json

When adding a new plugin, add its entry to the `plugins` array in `.claude-plugin/marketplace.json`. Keep the array ordered alphabetically by plugin name.

When modifying an existing plugin (adding skills, updating descriptions), bump the `version` field in both `marketplace.json` and the plugin's `plugin.json`.

## PR Checklist

Before submitting a pull request, verify the following:

- [ ] Plugin directory follows the structure: `plugins/<name>/.claude-plugin/plugin.json`
- [ ] `plugin.json` is valid JSON with `name`, `version`, `description`, and `author` fields
- [ ] Each skill has a `SKILL.md` file at `plugins/<name>/skills/<skill-name>/SKILL.md`
- [ ] Each `SKILL.md` has valid frontmatter with at least `name` and `description`
- [ ] The `SKILL.md` body includes a `## Steps` section (recommended: also include a `## Important` section for constraints)
- [ ] New plugins are registered in `.claude-plugin/marketplace.json`
- [ ] The `README.md` plugin catalog table and per-plugin sections are updated
- [ ] All names use kebab-case (lowercase letters and hyphens only)
- [ ] Descriptions are concise (one sentence) and start with a verb

## Style Guidelines

- **Names**: Always use kebab-case for plugin and skill names.
- **Descriptions**: Start with a verb, keep to one sentence, no trailing period.
- **Tools**: Only list tools the skill actually uses in `allowed-tools`.
- **Steps**: Be specific and actionable. Reference actual commands, file paths, and patterns.
- **Important**: Focus on safety constraints and things the skill must never do.
