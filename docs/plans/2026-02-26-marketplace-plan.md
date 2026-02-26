# Claude Code Community Marketplace — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Set up mambrose-claude-skills as a Claude Code marketplace repo with 18 skills grouped into 7 plugins.

**Architecture:** Git-based marketplace using Claude Code's native plugin system. A root `marketplace.json` catalogs 7 plugins, each containing grouped skills copied from `~/git/claude-skills/`.

**Tech Stack:** Claude Code plugin system (marketplace.json, plugin.json, SKILL.md)

---

### Task 1: Create marketplace.json

**Files:**
- Create: `.claude-plugin/marketplace.json`

**Step 1: Create the marketplace catalog**

Create `.claude-plugin/marketplace.json` with all 7 plugins listed. Use the schema from the official Anthropic marketplace. Each plugin entry needs: name, source (relative path), description, version, author, category.

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "mambrose-claude-skills",
  "description": "Community marketplace for Claude Code plugins — code quality, documentation, testing, and more",
  "owner": {
    "name": "mambrose"
  },
  "plugins": [
    {
      "name": "code-quality",
      "source": "./plugins/code-quality",
      "description": "Code review, bug finding, cleanup, naming checks, and secrets auditing",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "development"
    },
    {
      "name": "documentation",
      "source": "./plugins/documentation",
      "description": "Generate and maintain inline docs, API documentation, and READMEs",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "documentation"
    },
    {
      "name": "testing",
      "source": "./plugins/testing",
      "description": "Suggest new tests and raise test coverage",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "testing"
    },
    {
      "name": "project-tools",
      "source": "./plugins/project-tools",
      "description": "Suggest features, suggest skills, draft commits, and scaffold new skills",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "productivity"
    },
    {
      "name": "kotlin-tools",
      "source": "./plugins/kotlin-tools",
      "description": "Kotlin/Gradle utilities — generate DTOs from JSON and add Maven dependencies",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "development"
    },
    {
      "name": "hello-world",
      "source": "./plugins/hello-world",
      "description": "Generate a hello world program in any language",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "development"
    },
    {
      "name": "summarize-day",
      "source": "./plugins/summarize-day",
      "description": "Summarize accomplishments from git history with motivational messaging",
      "version": "1.0.0",
      "author": { "name": "mambrose" },
      "category": "productivity"
    }
  ]
}
```

**Step 2: Commit**

```bash
git add .claude-plugin/marketplace.json
git commit -m "feat: add marketplace.json with 7 plugin entries"
```

---

### Task 2: Create plugin directories and plugin.json files

**Files:**
- Create: `plugins/code-quality/.claude-plugin/plugin.json`
- Create: `plugins/documentation/.claude-plugin/plugin.json`
- Create: `plugins/testing/.claude-plugin/plugin.json`
- Create: `plugins/project-tools/.claude-plugin/plugin.json`
- Create: `plugins/kotlin-tools/.claude-plugin/plugin.json`
- Create: `plugins/hello-world/.claude-plugin/plugin.json`
- Create: `plugins/summarize-day/.claude-plugin/plugin.json`

**Step 1: Create all 7 plugin.json files**

Each plugin.json follows this minimal format (matching pambrose-plugins convention):

```json
{
  "name": "<plugin-name>",
  "version": "1.0.0",
  "description": "<description>",
  "author": {
    "name": "mambrose"
  }
}
```

Use these descriptions:
- code-quality: "Code review, bug finding, cleanup, naming checks, and secrets auditing"
- documentation: "Generate and maintain inline docs, API documentation, and READMEs"
- testing: "Suggest new tests and raise test coverage"
- project-tools: "Suggest features, suggest skills, draft commits, and scaffold new skills"
- kotlin-tools: "Kotlin/Gradle utilities — generate DTOs from JSON and add Maven dependencies"
- hello-world: "Generate a hello world program in any language"
- summarize-day: "Summarize accomplishments from git history with motivational messaging"

**Step 2: Commit**

```bash
git add plugins/*/. claude-plugin/plugin.json
git commit -m "feat: add plugin.json for all 7 plugins"
```

---

### Task 3: Port skills — code-quality plugin

**Files:**
- Copy: `~/git/claude-skills/find-bugs/SKILL.md` → `plugins/code-quality/skills/find-bugs/SKILL.md`
- Copy: `~/git/claude-skills/fix-bugs/SKILL.md` → `plugins/code-quality/skills/fix-bugs/SKILL.md`
- Copy: `~/git/claude-skills/clean-up/SKILL.md` → `plugins/code-quality/skills/clean-up/SKILL.md`
- Copy: `~/git/claude-skills/check-naming/SKILL.md` → `plugins/code-quality/skills/check-naming/SKILL.md`
- Copy: `~/git/claude-skills/check-secrets/SKILL.md` → `plugins/code-quality/skills/check-secrets/SKILL.md`

**Step 1: Copy all 5 SKILL.md files**

```bash
for skill in find-bugs fix-bugs clean-up check-naming check-secrets; do
  mkdir -p plugins/code-quality/skills/$skill
  cp ~/git/claude-skills/$skill/SKILL.md plugins/code-quality/skills/$skill/SKILL.md
done
```

**Step 2: Commit**

```bash
git add plugins/code-quality/skills/
git commit -m "feat: port code-quality skills (find-bugs, fix-bugs, clean-up, check-naming, check-secrets)"
```

---

### Task 4: Port skills — documentation plugin

**Files:**
- Copy: `~/git/claude-skills/add-docs/SKILL.md` → `plugins/documentation/skills/add-docs/SKILL.md`
- Copy: `~/git/claude-skills/update-docs/SKILL.md` → `plugins/documentation/skills/update-docs/SKILL.md`
- Copy: `~/git/claude-skills/update-readme/SKILL.md` → `plugins/documentation/skills/update-readme/SKILL.md`

**Step 1: Copy all 3 SKILL.md files**

```bash
for skill in add-docs update-docs update-readme; do
  mkdir -p plugins/documentation/skills/$skill
  cp ~/git/claude-skills/$skill/SKILL.md plugins/documentation/skills/$skill/SKILL.md
done
```

**Step 2: Commit**

```bash
git add plugins/documentation/skills/
git commit -m "feat: port documentation skills (add-docs, update-docs, update-readme)"
```

---

### Task 5: Port skills — testing plugin

**Files:**
- Copy: `~/git/claude-skills/suggest-tests/SKILL.md` → `plugins/testing/skills/suggest-tests/SKILL.md`
- Copy: `~/git/claude-skills/raise-coverage/SKILL.md` → `plugins/testing/skills/raise-coverage/SKILL.md`

**Step 1: Copy both SKILL.md files**

```bash
for skill in suggest-tests raise-coverage; do
  mkdir -p plugins/testing/skills/$skill
  cp ~/git/claude-skills/$skill/SKILL.md plugins/testing/skills/$skill/SKILL.md
done
```

**Step 2: Commit**

```bash
git add plugins/testing/skills/
git commit -m "feat: port testing skills (suggest-tests, raise-coverage)"
```

---

### Task 6: Port skills — project-tools plugin

**Files:**
- Copy: `~/git/claude-skills/suggest-features/SKILL.md` → `plugins/project-tools/skills/suggest-features/SKILL.md`
- Copy: `~/git/claude-skills/suggest-skills/SKILL.md` → `plugins/project-tools/skills/suggest-skills/SKILL.md`
- Copy: `~/git/claude-skills/suggest-commit/SKILL.md` → `plugins/project-tools/skills/suggest-commit/SKILL.md`
- Copy: `~/git/claude-skills/create-skill/SKILL.md` → `plugins/project-tools/skills/create-skill/SKILL.md`

**Step 1: Copy all 4 SKILL.md files**

```bash
for skill in suggest-features suggest-skills suggest-commit create-skill; do
  mkdir -p plugins/project-tools/skills/$skill
  cp ~/git/claude-skills/$skill/SKILL.md plugins/project-tools/skills/$skill/SKILL.md
done
```

**Step 2: Commit**

```bash
git add plugins/project-tools/skills/
git commit -m "feat: port project-tools skills (suggest-features, suggest-skills, suggest-commit, create-skill)"
```

---

### Task 7: Port skills — kotlin-tools, hello-world, summarize-day

**Files:**
- Copy: `~/git/claude-skills/add-dto/SKILL.md` → `plugins/kotlin-tools/skills/add-dto/SKILL.md`
- Copy: `~/git/claude-skills/add-dependency/SKILL.md` → `plugins/kotlin-tools/skills/add-dependency/SKILL.md`
- Copy: `~/git/claude-skills/hello-world/SKILL.md` → `plugins/hello-world/skills/hello-world/SKILL.md`
- Copy: `~/git/claude-skills/summarize-day/SKILL.md` → `plugins/summarize-day/skills/summarize-day/SKILL.md`

**Step 1: Copy all 4 SKILL.md files**

```bash
for skill in add-dto add-dependency; do
  mkdir -p plugins/kotlin-tools/skills/$skill
  cp ~/git/claude-skills/$skill/SKILL.md plugins/kotlin-tools/skills/$skill/SKILL.md
done

mkdir -p plugins/hello-world/skills/hello-world
cp ~/git/claude-skills/hello-world/SKILL.md plugins/hello-world/skills/hello-world/SKILL.md

mkdir -p plugins/summarize-day/skills/summarize-day
cp ~/git/claude-skills/summarize-day/SKILL.md plugins/summarize-day/skills/summarize-day/SKILL.md
```

**Step 2: Commit**

```bash
git add plugins/kotlin-tools/ plugins/hello-world/ plugins/summarize-day/
git commit -m "feat: port kotlin-tools, hello-world, and summarize-day skills"
```

---

### Task 8: Create README.md

**Files:**
- Create: `README.md`

**Step 1: Write the README**

Include:
- Marketplace name and one-line description
- Installation instructions (`/plugin marketplace add mambrose/mambrose-claude-skills`)
- Plugin catalog table (name, description, skills, category)
- Per-plugin sections with skill details
- How to install individual plugins (`/plugin install code-quality@mambrose-claude-skills`)

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with plugin catalog and installation instructions"
```

---

### Task 9: Create CONTRIBUTING.md

**Files:**
- Create: `CONTRIBUTING.md`

**Step 1: Write contribution guide**

Cover:
- How to create a new plugin (directory structure, plugin.json, SKILL.md)
- How to add a skill to an existing plugin
- PR checklist (valid plugin.json, SKILL.md with frontmatter, added to marketplace.json)
- Style guidelines (kebab-case names, descriptions, allowed-tools)

**Step 2: Commit**

```bash
git add CONTRIBUTING.md
git commit -m "docs: add contribution guidelines"
```

---

### Task 10: Update .gitignore and final verification

**Files:**
- Modify: `.gitignore`

**Step 1: Verify marketplace structure**

```bash
find . -name "marketplace.json" -o -name "plugin.json" -o -name "SKILL.md" | sort
```

Expected: 1 marketplace.json + 7 plugin.json + 18 SKILL.md = 26 files

**Step 2: Commit any final changes**

```bash
git add -A
git commit -m "chore: finalize marketplace structure"
```
