# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code marketplace repository. Users add it with `/plugin marketplace add mattbobambrose/mattbobambrose-claude-skills` and install plugins via the `/plugin` interface. There is no build system, no tests, and no application code — only plugin metadata and skill definitions.

## Architecture

- `.claude-plugin/marketplace.json` — root catalog listing all plugins (this is what Claude Code reads to discover available plugins)
- `plugins/<name>/.claude-plugin/plugin.json` — per-plugin metadata (name, version, description, author)
- `plugins/<name>/skills/<skill-name>/SKILL.md` — skill definitions with YAML frontmatter and markdown body

This repo is the **source of truth** for all skills. There is no separate skills directory or symlink.

## MANDATORY: Version Bumps on Every Change

**Any time a plugin's content is added, modified, or removed, you MUST bump its version in BOTH:**

1. `plugins/<name>/.claude-plugin/plugin.json` — the `"version"` field
2. `.claude-plugin/marketplace.json` — the matching plugin's `"version"` field

Use semver: patch (1.0.0 → 1.0.1) for skill edits, minor (1.0.0 → 1.1.0) for new skills, major for breaking changes.

**Claude Code uses the version field to detect updates.** If you don't bump it, users will never see the change.

## When Modifying a Skill

1. Edit the `SKILL.md` file
2. Bump version in `plugins/<name>/.claude-plugin/plugin.json`
3. Bump version in `.claude-plugin/marketplace.json`

## When Adding a Skill to an Existing Plugin

1. Create `plugins/<name>/skills/<skill-name>/SKILL.md`
2. Bump version (minor) in `plugins/<name>/.claude-plugin/plugin.json`
3. Bump version (minor) in `.claude-plugin/marketplace.json`
4. Update `README.md` — add skill to the plugin's table and update the skill count in the catalog table

## When Adding a New Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` (start at version 1.0.0) and `plugins/<name>/skills/` directory
2. Add at least one skill
3. Add the plugin entry to `.claude-plugin/marketplace.json` (keep alphabetical order)
4. Add the plugin to `README.md` catalog table and create a per-plugin section

## Conventions

- All names use kebab-case
- SKILL.md frontmatter must have `name` and `description`; `argument-hint` and `allowed-tools` are optional
- Descriptions start with a verb, one sentence, no trailing period
- Valid plugin categories: `development`, `documentation`, `testing`, `productivity`
- See CONTRIBUTING.md for the full PR checklist and SKILL.md template

## Validation

To verify marketplace integrity:

```bash
find . -name "marketplace.json" -o -name "plugin.json" -o -name "SKILL.md" | sort
```

To test a plugin locally without installing:

```bash
claude --plugin-dir ./plugins/<plugin-name>
```
