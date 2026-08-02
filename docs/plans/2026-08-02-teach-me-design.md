# Design: learning Plugin (`/teach-me`, `/next-lesson`, `/quiz-me`)

**Date:** 2026-08-02
**Plugin:** learning (new)
**Type:** New plugin with three skills

## Purpose

Turn a bare directory or empty repo into a personalized, self-paced course on any subject. The user runs
`/teach-me <topic>`, answers two short rounds of questions, and gets a structured learning repo: a full lesson plan,
a deep context/history module, a fully written first module, and outlined stubs for everything else.
`/next-lesson` fills in the next module on demand; `/quiz-me` tests what has been written and records the results.

The motivating example is a repo built to learn OCaml for a programming languages class, taught as if for a job —
including history, applications, why the language exists, and why it is used.

## Goals

- Work for any subject, not just programming — sports, cooking, fitness, academic courses, crafts, professions.
- Ask questions specific to the *class* of topic, not generic ones.
- Produce a repo that is immediately usable (not just an empty skeleton) without waiting on a full course generation.
- Keep later modules adaptive — they are written after the learner has progressed, informed by stored interview answers.
- Never fabricate facts or links in material intended for learning.

## Non-Goals

- No spaced-repetition scheduling, no streaks, no gamification.
- No hosting, publishing, or syncing of the generated repo.
- No auto-commit or auto-push of generated material.
- No grading integration with external LMS or course platforms.

## Packaging

New plugin `learning`, category `productivity`, version `1.0.0`.

```
plugins/learning/
├── .claude-plugin/plugin.json
└── skills/
    ├── teach-me/
    │   ├── SKILL.md
    │   └── references/
    │       ├── topic-classes.md              # classifier rules + index of classes
    │       ├── class-programming-language.md
    │       ├── class-technology.md
    │       ├── class-academic-subject.md
    │       ├── class-physical-skill.md
    │       ├── class-fitness.md
    │       ├── class-cooking.md
    │       ├── class-creative-craft.md
    │       ├── class-professional-domain.md
    │       └── class-generic.md
    ├── next-lesson/SKILL.md
    └── quiz-me/SKILL.md
```

`next-lesson` and `quiz-me` are self-contained single files. They do not read the `references/` directory. They derive
everything they need from the generated repo itself: `.teach-me/course.md` for state and interview answers, and the
already-written modules as style, depth, and format exemplars. This avoids cross-skill file path coupling and
`${CLAUDE_PLUGIN_ROOT}` gymnastics.

## Topic Classes

Nine classes. Each `class-*.md` reference file contains exactly three sections: **Round 2 Questions**,
**Folder Layout**, and **Lesson Shape**.

| Class | Example topics |
|-------|----------------|
| `programming-language` | Rust, OCaml, Kotlin, SQL |
| `technology` | React, Kubernetes, Postgres, Git |
| `academic-subject` | linear algebra, organic chemistry, macroeconomics |
| `physical-skill` | tennis, climbing, surfing, chess |
| `fitness` | marathon training, powerlifting, mobility |
| `cooking` | bread baking, knife skills, Thai food |
| `creative-craft` | guitar, watercolor, fiction writing |
| `professional-domain` | accounting, product management, contract law |
| `generic` | anything unmatched — still produces a working course |

Classification is a judgment call made by reading `topic-classes.md`. The guess is stated to the user in one line and
can be corrected before any questions are asked. Ambiguous topics (e.g. "SQL" could be `programming-language` or
`technology`) default to the class listed first in `topic-classes.md` and are always surfaced for confirmation.

## `/teach-me [topic]`

`argument-hint: "[topic]"`

Allowed tools: `Read`, `Write`, `Edit`, `Bash`, `Glob`, `WebSearch`, `WebFetch`, `AskUserQuestion`.

### Steps

1. **Determine the topic** — from `$ARGUMENTS`. If empty, ask what the user wants to learn.

2. **Classify the topic** — read `references/topic-classes.md`, pick a class, state it in one line
   ("Treating *bread baking* as a **cooking** topic — say so if you'd rather it be something else"), and let the user
   correct it. Then read the matching `class-*.md` file.

3. **Round 1 — universal questions.** One `AskUserQuestion` batch of four:
   - **Motivation** — job / class or exam / personal project / curiosity
   - **Current level** — none / some exposure / rusty / experienced in an adjacent area
   - **Time budget** — a weekend / a few weeks / a semester / ongoing
   - **Style** — hands-on first / theory first / balanced

   Free-text answers via "Other" are expected and must be preserved verbatim in `course.md`. The OCaml case
   ("for a PL class, but teach it as if for a job") is exactly this: motivation is dual, and both halves must survive
   into the stored answers and shape the material.

4. **Round 2 — class-specific questions.** One `AskUserQuestion` batch of roughly four, taken from the class file's
   **Round 2 Questions** section.

5. **Resolve the target directory:**

   | Current directory state | Behavior |
   |---|---|
   | Empty, or only `.git/`, `README.md`, `LICENSE`, `.gitignore` | Fill in place |
   | Contains other content | Propose `./<topic-slug>-learning/` and confirm before writing |

   Run `git init` only if no repository is present anywhere up the tree. Never stage, commit, or push.
   Never overwrite an existing file without explicit confirmation.

6. **Draft and confirm the lesson plan.** Module count scales to the time budget:

   | Time budget | Modules (excluding `00-context`) |
   |---|---|
   | A weekend | 4–5 |
   | A few weeks | 6–8 |
   | A semester | 10–14 |
   | Ongoing | 12+ |

   The last module is always a capstone. Show the ordered module list with one-line descriptions and get approval
   before writing anything.

7. **Write the repo** (see next section). `00-context/` and module `01` are written in full; every other module is
   outlined.

8. **Report** — what was created, where, and that `/next-lesson` writes the next module.

## Generated Repo Layout

```
README.md              lesson plan, prerequisites, how to use this repo, module index
resources.md           books, courses, communities, docs — verified links only
.teach-me/course.md    machine + human state (see below)
00-context/README.md   FULL
01-<slug>/             FULL
  README.md            the lesson
  exercises/           problem statements
  solutions/           worked solutions
02-<slug>/README.md    OUTLINED
...
NN-capstone/README.md  OUTLINED — final project brief
```

`00-context/README.md` is always written in full and always covers: origin and history, who created it and why,
what problems it solves, where it is actually used today, the surrounding ecosystem, honest tradeoffs and criticisms,
and how it compares to its main alternatives.

`exercises/` + `solutions/` is the `programming-language` and `technology` shape. Other classes substitute their own
practice directories, defined in the class file:

| Class | Practice directories |
|---|---|
| `academic-subject` | `notes/`, `problem-sets/` |
| `physical-skill` | `drills/`, `practice-log/` |
| `fitness` | `programs/`, `logs/` |
| `cooking` | `techniques/`, `recipes/` |
| `creative-craft` | `studies/`, `portfolio/` |
| `professional-domain` | `case-studies/`, `worksheets/` |
| `generic` | `exercises/`, `notes/` |

### Outlined module stubs

Every outlined module's `README.md` contains: title, learning objectives, a topic outline, prerequisites (which
module must come first), estimated time, and a closing line stating it has not been written yet and that
`/next-lesson` will fill it in.

## Course State: `.teach-me/course.md`

YAML frontmatter for machine state, markdown body for a human-tickable progress checklist.

```markdown
---
topic: OCaml
slug: ocaml
class: programming-language
created: 2026-08-02
answers:
  motivation: "PL class, but teach it as if I was learning it for a job"
  level: none
  time_budget: semester
  style: hands-on
  prior_languages: [Python, Java]
  target_domain: compilers
  toolchain_setup: true
  idiomatic_depth: production
modules:
  - {id: "00-context",  title: "History and Context",   status: written}
  - {id: "01-basics",   title: "Basics",                status: written, completed: true}
  - {id: "02-syntax",   title: "Syntax and Types",      status: outlined}
  - {id: "03-modules",  title: "Modules and Functors",  status: outlined}
---

## Progress

- [x] 00 History and Context
- [x] 01 Basics
- [ ] 02 Syntax and Types
- [ ] 03 Modules and Functors
```

`status` is `written` or `outlined`. `completed` is set to `true` by `/quiz-me` on a strong score, or by the user
by hand. The body checklist mirrors `completed` and is rewritten whenever status changes.

If `.teach-me/course.md` is missing or unparseable, `/next-lesson` and `/quiz-me` fall back to inferring state from
the filesystem (which module directories have real lesson content vs. stubs) and offer to regenerate the file.

## `/next-lesson [module]`

`argument-hint: "[module]"`

Allowed tools: `Read`, `Write`, `Edit`, `Bash`, `Glob`, `WebSearch`, `WebFetch`.

### Steps

1. **Locate the course** — find `.teach-me/course.md` in the current directory, then walk up the tree. If not found,
   report that this is not a `/teach-me` repo and stop.
2. **Pick the module** — `$ARGUMENTS` may name a module id, a number, or a title fragment. With no argument, take the
   first module with `status: outlined`. If all modules are written, say so and stop.
3. **Load context** — the interview answers from frontmatter, the target module's outline stub, and the
   highest-numbered module with `status: written` read in full as a voice, depth, and formatting exemplar.
4. **Ground selectively** — search the web only if this module covers version-specific, tooling, or safety-sensitive
   material. Verify any Further Reading links before writing them.
5. **Write the module** in full: lesson prose, worked examples, and the class's practice directories populated.
6. **Update state** — set `status: written`, rewrite the progress checklist, and update the README module index.
7. **Report** what was written and which module is next.

## `/quiz-me [module]`

`argument-hint: "[module]"`

Allowed tools: `Read`, `Write`, `Edit`, `Bash`, `Glob`, `AskUserQuestion`.

### Steps

1. **Locate the course** and collect modules with `status: written`.
2. **Scope the quiz** — one `AskUserQuestion` batch with two questions: scope (the highest-numbered written module /
   all written modules / a specific module) and length (5 / 10 / 20 questions). `$ARGUMENTS` naming a module skips
   the scope question and quizzes that module only.
3. **Generate questions** with a class-aware mix:

   | Class | Question types |
   |---|---|
   | `programming-language`, `technology` | recall, predict-the-output, fix-the-bug, "how would you implement" |
   | `academic-subject` | definitions, derivations, applied problems |
   | `cooking`, `physical-skill`, `fitness` | technique recall, decision scenarios, diagnose-the-failure |
   | `creative-craft`, `professional-domain` | concept recall, critique, applied judgment scenarios |
   | `generic` | recall, application, comparison |

4. **Ask interactively**, one question per message. Grade each answer as correct / partial / incorrect, and on anything
   less than correct, explain the right answer and point at the module section that covers it. Never just mark it wrong.
5. **Record the session** to `.teach-me/quizzes/<YYYY-MM-DD>-<scope>.md`, where `<scope>` is the module id for a
   single-module quiz or `all` for a multi-module quiz. Record questions, the user's answers, grades, final score,
   and identified weak spots. Use `date +%Y-%m-%d` via Bash for the filename. If a file with that name already
   exists, append `-2`, `-3`, and so on.
6. **Update state** — mark a module `completed: true` at 80% or better on the questions drawn from that module, and
   update the progress checklist. Below that, report which sections to revisit.

## Grounding and Accuracy

Web search is used selectively, for the content most likely to be wrong or stale:

| Searched | From model knowledge |
|---|---|
| `00-context` — dates, creators, adoption claims | core concepts and explanations |
| setup, install steps, current stable versions | syntax, worked examples |
| current best practices | exercises and solutions |
| `fitness` — programming and injury guidance | quiz questions |
| `cooking` — food-safety temperatures and times | |
| Further Reading links in every module | |

Further Reading links must be fetched and confirmed to exist. Inventing plausible-looking URLs is prohibited.

## Safety Constraints

- Never overwrite an existing file without explicit confirmation.
- Never stage, commit, or push. `git init` is the only git operation, and only when no repository exists.
- `fitness` and `cooking` courses include a `DISCLAIMER.md` at the repo root.
- `fitness` material must not prescribe medical, rehabilitative, or nutritional treatment; it stays at the level of
  general training principles and defers to professionals for injury and diet.
- `cooking` food-safety figures (temperatures, times, storage) must be cited to a named authoritative source.
- `professional-domain` material covering law, accounting, or finance carries the same deference: general education,
  not professional advice for a specific situation.

## Marketplace Registration

Per `CLAUDE.md`, all of the following are required:

1. `plugins/learning/.claude-plugin/plugin.json` at version `1.0.0`.
2. Entry in `.claude-plugin/marketplace.json`, inserted between `hello-world` and `linear-bug-sweep`
   (alphabetically correct against both neighbors), category `productivity`, version `1.0.0`.
3. `README.md` — a row in the plugin catalog table (3 skills) and a new per-plugin section listing all three skills.

Since this is a new plugin at `1.0.0`, no version bumps to existing plugins are involved.

## Open Risks

- **Generation volume.** A semester-length course writes `00-context`, module `01` with exercises and solutions,
  plus 12+ stubs in one run. If this proves slow in practice, the mitigation is trimming stub content, not deferring
  module `01`.
- **Class boundaries.** `programming-language` vs `technology` and `physical-skill` vs `fitness` will both see
  ambiguous topics. The confirmation step in step 2 is the mitigation; the class files should overlap gracefully
  rather than assume a clean split.
- **Exemplar drift.** `/next-lesson` matching the style of the previous module means an early formatting mistake
  propagates through the whole course. Accepted: the user can edit module `01` and later modules will follow the fix.
