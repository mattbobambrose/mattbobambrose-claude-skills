# learning Plugin — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a `learning` plugin to the marketplace providing `/teach-me`, `/next-lesson`, and `/quiz-me`, which turn an empty directory into a personalized course repo on any subject.

**Architecture:** One new plugin directory with three skills. `/teach-me` interviews the user in two batched rounds (universal, then class-specific from a `references/class-*.md` file), then writes a course repo with `00-context/` and module `01` fully written and the rest outlined. `/next-lesson` and `/quiz-me` are self-contained single files that read the generated repo's `.teach-me/course.md` state file and the already-written modules — they never read the plugin's `references/` directory.

**Tech Stack:** Markdown skill definitions and JSON manifests. No build system, no test framework.

**Spec:** `docs/plans/2026-08-02-teach-me-design.md`

## Global Constraints

- All names kebab-case. Descriptions start with a verb, one sentence, no trailing period.
- `SKILL.md` frontmatter requires `name` and `description`; `argument-hint` is quoted; `allowed-tools` is a YAML list written in this repo's style — a blank line after `allowed-tools:`, then `- Tool` items, then a blank line before the closing `---`.
- Every `SKILL.md` body has a `## Steps` section and a `## Important` section.
- New plugin starts at version `1.0.0` in **both** `plugins/learning/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
- Valid categories: `development`, `documentation`, `testing`, `productivity`. This plugin is `productivity`.
- **No commits until the user explicitly asks.** Tasks 1–10 write files only. Task 11 is gated on user instruction.

## Verification Approach

There is no test framework in this repo, so "write a failing test first" is not available. Each task instead ends with a verification command that genuinely fails if the task was done wrong. Run every verification command and read its output before marking a step complete.

## File Structure

| File | Responsibility |
|---|---|
| `plugins/learning/.claude-plugin/plugin.json` | Plugin manifest |
| `plugins/learning/skills/teach-me/SKILL.md` | Interview, classify, scaffold the course repo |
| `plugins/learning/skills/teach-me/references/topic-classes.md` | Class list + classification rules |
| `plugins/learning/skills/teach-me/references/class-*.md` (9 files) | Per-class round-2 questions, folder layout, lesson shape |
| `plugins/learning/skills/next-lesson/SKILL.md` | Write the next outlined module |
| `plugins/learning/skills/quiz-me/SKILL.md` | Quiz on written modules, record results |
| `.claude-plugin/marketplace.json` | Register the plugin |
| `README.md` | Catalog row + per-plugin section |

---

### Task 1: Plugin manifest and directory skeleton

**Files:**
- Create: `plugins/learning/.claude-plugin/plugin.json`

**Interfaces:**
- Produces: plugin name `learning`, version `1.0.0` — Task 9 must match these exactly in `marketplace.json`.

- [ ] **Step 1: Create the directory tree**

```bash
mkdir -p plugins/learning/.claude-plugin \
         plugins/learning/skills/teach-me/references \
         plugins/learning/skills/next-lesson \
         plugins/learning/skills/quiz-me
```

- [ ] **Step 2: Write the manifest**

Write this exact content to `plugins/learning/.claude-plugin/plugin.json`:

```json
{
  "name": "learning",
  "version": "1.0.0",
  "description": "Build a personalized course repo on any subject, then write lessons and quiz yourself",
  "author": {
    "name": "mattbobambrose"
  }
}
```

- [ ] **Step 3: Verify the JSON parses and the tree exists**

Run:
```bash
python3 -m json.tool plugins/learning/.claude-plugin/plugin.json && \
find plugins/learning -type d | sort
```
Expected: the JSON pretty-printed with no error, then exactly these seven directories:
```
plugins/learning
plugins/learning/.claude-plugin
plugins/learning/skills
plugins/learning/skills/next-lesson
plugins/learning/skills/quiz-me
plugins/learning/skills/teach-me
plugins/learning/skills/teach-me/references
```

---

### Task 2: `/teach-me` SKILL.md

**Files:**
- Create: `plugins/learning/skills/teach-me/SKILL.md`

**Interfaces:**
- Consumes: nothing from earlier tasks.
- Produces: the contract every `references/class-*.md` file must satisfy — each class file has exactly three `##` sections named **Round 2 Questions**, **Folder Layout**, and **Lesson Shape**. Tasks 4, 5, and 6 depend on these exact names. Also produces the `.teach-me/course.md` frontmatter schema that Tasks 7 and 8 read.

- [ ] **Step 1: Write the skill**

Write this exact content to `plugins/learning/skills/teach-me/SKILL.md`:

````markdown
---
name: teach-me
description: Build a personalized course repo that teaches a subject from history and fundamentals through advanced application
argument-hint: "[topic]"
allowed-tools:

- Read
- Write
- Edit
- Bash
- Glob
- WebSearch
- WebFetch
- AskUserQuestion

---

Turn an empty directory or repo into a personalized, self-paced course on any subject — a programming language, an
academic course, a sport, a recipe repertoire, a training program, a craft, or a profession. Interviews the user in two
short rounds, then writes a structured learning repo with a full lesson plan, a deep history-and-context module, a
fully written first module, and outlined stubs for the rest. `/next-lesson` fills in later modules on demand.

**$ARGUMENTS** is the topic to learn. If empty, ask the user what they want to learn before doing anything else.

## Steps

1. **Determine the topic**: Use `$ARGUMENTS`. If empty, ask what the user wants to learn and wait for an answer.

2. **Classify the topic**: Read `references/topic-classes.md` and pick the best-matching class. State the guess in one
   line and invite correction, e.g. "Treating *bread baking* as a **cooking** topic — say so if you'd rather it be
   something else." Then read the matching `references/class-<class>.md` file in full. Do not proceed until the class
   is settled.

3. **Round 1 — universal questions**: Ask these four in a single `AskUserQuestion` batch.

    | Question | Options |
    |----------|---------|
    | Why are you learning this? | For a job / For a class or exam / For a personal project / Curiosity |
    | What's your current level? | None / Some exposure / Rusty — knew it once / Experienced in something adjacent |
    | How much time do you have? | A weekend / A few weeks / A semester / Ongoing, no deadline |
    | How do you learn best? | Hands-on first / Theory first / Balanced |

    Preserve free-text "Other" answers verbatim — they are often the most important input. A motivation like
    "for a PL class, but teach it as if I were learning it for a job" is dual, and **both halves must shape the
    material**: the academic angle deepens `00-context`, the job angle shapes exercises and the capstone.

4. **Round 2 — class-specific questions**: Ask the questions from the class file's **Round 2 Questions** section in a
   single `AskUserQuestion` batch.

5. **Resolve the target directory**:

    | Current directory state | Action |
    |---|---|
    | Empty, or contains only `.git/`, `README.md`, `LICENSE`, `.gitignore` | Write in place |
    | Contains any other content | Propose `./<topic-slug>-learning/` and confirm before writing |

    Check for a git repository with `git rev-parse --git-dir`. If there is none, run `git init` in the target
    directory. Never stage, commit, or push.

6. **Draft the lesson plan and confirm it**: Choose the module count from the time budget.

    | Time budget | Modules after `00-context` |
    |---|---|
    | A weekend | 4–5 |
    | A few weeks | 6–8 |
    | A semester | 10–14 |
    | Ongoing | 12+ |

    Order modules so each depends only on earlier ones. The final module is always a capstone. Show the ordered list
    with a one-line description per module and get approval before writing any files.

7. **Write the repo**:

    ```
    README.md              lesson plan, prerequisites, how to use this repo, module index
    resources.md           books, courses, communities, docs — verified links only
    .teach-me/course.md    course state
    00-context/README.md   FULL
    01-<slug>/             FULL — README.md plus the class's practice directories
    02-<slug>/README.md    OUTLINED
    ...
    NN-capstone/README.md  OUTLINED
    ```

    - `00-context/README.md` is always written in full and always covers: origin and history, who created it and why,
      what problems it solves, where it is used today, the surrounding ecosystem, honest tradeoffs and criticisms, and
      how it compares to its main alternatives.
    - Module `01` is written in full, following the section order given in the class file's **Lesson Shape** section:
      lesson prose, worked examples, and the practice directories named in the class file's **Folder Layout** section,
      populated with real content.
    - Every other module gets a stub `README.md` containing: title, learning objectives, a topic outline,
      prerequisites (which module comes first), estimated time, and a closing line stating it has not been written yet
      and that `/next-lesson` will fill it in.
    - Classes `fitness` and `cooking` also get a `DISCLAIMER.md` at the repo root, per their class file.

8. **Write `.teach-me/course.md`**: YAML frontmatter for state, markdown body for a human-tickable checklist.

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
    modules:
      - {id: "00-context", title: "History and Context", status: written}
      - {id: "01-basics",  title: "Basics",              status: written}
      - {id: "02-syntax",  title: "Syntax and Types",    status: outlined}
    ---

    ## Progress

    - [x] 00 History and Context
    - [x] 01 Basics
    - [ ] 02 Syntax and Types
    ```

    - `status` is `written` or `outlined`. `completed: true` is added later by `/quiz-me` or by the user.
    - Record every round-1 and round-2 answer under `answers`, using the class file's key names.
    - Get the date from `date +%Y-%m-%d` via Bash — never guess it.

9. **Ground the material selectively**: Use WebSearch and WebFetch for the content most likely to be wrong or stale —
   `00-context` dates, creators, and adoption claims; install steps and current stable versions; current best
   practices; and the safety-sensitive content called out in the `fitness` and `cooking` class files. Core concepts,
   worked examples, and exercises come from your own knowledge. Every Further Reading link must be fetched and
   confirmed to exist before you write it down.

10. **Report**: State what was created and where, which modules are written vs outlined, and that `/next-lesson`
    writes the next module and `/quiz-me` tests what exists.

## Important

- Never overwrite an existing file without explicit confirmation.
- Never stage, commit, or push. `git init` is the only permitted git write operation, and only when no repository
  exists.
- Never invent a URL. If a link cannot be verified, describe the resource by name instead of linking it.
- Do not write later modules in full. Outlined stubs are deliberate — they let `/next-lesson` adapt to the learner's
  actual progress.
- Get approval on the lesson plan in step 6 before writing any course files.
- `fitness` and `cooking` courses must include `DISCLAIMER.md`. Fitness material stays at the level of general
  training principles and never prescribes medical, rehabilitative, or nutritional treatment. Cooking food-safety
  figures must be cited to a named authoritative source.
````

- [ ] **Step 2: Verify frontmatter and required sections**

Run:
```bash
head -16 plugins/learning/skills/teach-me/SKILL.md && \
grep -c '^## Steps$\|^## Important$' plugins/learning/skills/teach-me/SKILL.md
```
Expected: frontmatter with `name: teach-me` and `argument-hint: "[topic]"`, then `2`.

- [ ] **Step 3: Verify the class-file contract is stated**

Run: `grep -n 'Round 2 Questions\|Folder Layout\|Lesson Shape' plugins/learning/skills/teach-me/SKILL.md`
Expected: at least one hit for each of the three section names. These are the names Tasks 4–6 must use.

---

### Task 3: `references/topic-classes.md`

**Files:**
- Create: `plugins/learning/skills/teach-me/references/topic-classes.md`

**Interfaces:**
- Consumes: read by `/teach-me` step 2.
- Produces: the nine class ids used as `class-<id>.md` filenames and as the `class:` value in `course.md`.

- [ ] **Step 1: Write the classifier**

Write this exact content to `plugins/learning/skills/teach-me/references/topic-classes.md`:

````markdown
# Topic Classes

Pick the class whose examples the topic most resembles, then read `class-<id>.md`.

| Class id | Covers | Example topics |
|----------|--------|----------------|
| `programming-language` | A language, its syntax, semantics, and idioms | Rust, OCaml, Kotlin, SQL, Haskell |
| `technology` | A framework, tool, platform, or system built with languages | React, Kubernetes, Postgres, Git, Terraform |
| `academic-subject` | A body of theory taught as coursework | linear algebra, organic chemistry, macroeconomics |
| `physical-skill` | A skill practiced through drilling and competition | tennis, climbing, surfing, chess, juggling |
| `fitness` | Training the body toward a capacity goal | marathon training, powerlifting, mobility |
| `cooking` | Food preparation techniques and repertoire | bread baking, knife skills, Thai food |
| `creative-craft` | An expressive craft built through practice and critique | guitar, watercolor, fiction writing |
| `professional-domain` | Applied professional knowledge | accounting, product management, contract law |
| `generic` | Anything the classes above do not fit | — |

## Rules

- **Language vs technology.** If the topic is a language you write code *in*, use `programming-language`. If it is a
  thing you use *from* a language, use `technology`. SQL is `programming-language`. Postgres is `technology`.
- **Physical skill vs fitness.** If progress is measured by technique against an opponent, a route, or a board, use
  `physical-skill`. If it is measured by a physical capacity like distance, load, or time, use `fitness`. Chess is
  `physical-skill` despite being sedentary — it is drilled and competed the same way.
- **Academic subject vs professional domain.** If the goal is understanding a body of theory, use `academic-subject`.
  If the goal is performing a job function, use `professional-domain`. Someone learning accounting for a CPA exam is
  still `professional-domain` — the material is the job.
- **Ambiguity.** When two classes fit, choose the one listed higher in the table and say so when you state the guess.
  The user corrects it in one word if you are wrong.
- **Never force a fit.** `generic` produces a real course. Choosing it is not a failure.
````

- [ ] **Step 2: Verify all nine class ids are present**

Run:
```bash
for c in programming-language technology academic-subject physical-skill fitness cooking creative-craft professional-domain generic; do
  grep -q "\`$c\`" plugins/learning/skills/teach-me/references/topic-classes.md \
    && echo "ok   $c" || echo "MISSING $c"
done
```
Expected: nine `ok` lines, no `MISSING`.

---

### Task 4: Class files — `programming-language` and `technology`

**Files:**
- Create: `plugins/learning/skills/teach-me/references/class-programming-language.md`
- Create: `plugins/learning/skills/teach-me/references/class-technology.md`

**Interfaces:**
- Consumes: the three-section contract from Task 2.
- Produces: the `answers` keys `prior_languages`, `target_domain`, `toolchain_setup`, `idiomatic_depth` (programming-language) and `prior_stack`, `use_case`, `depth`, `ops_scope` (technology), written into `course.md` by `/teach-me`.

- [ ] **Step 1: Write `class-programming-language.md`**

This file is the **exemplar** for all other class files. Write it exactly:

````markdown
# Class: programming-language

## Round 2 Questions

| Question | Options | `answers` key |
|----------|---------|---------------|
| Which languages do you already know? | (free text — expect a list) | `prior_languages` |
| What do you want to build with it? | Web services / Systems and tooling / Data and analysis / Compilers and languages | `target_domain` |
| Should I set up the toolchain? | Yes, configure this repo to compile and run / No, just teach me the language | `toolchain_setup` |
| How deep on idioms? | Enough to read code / Enough to ship production code | `idiomatic_depth` |

`prior_languages` is the highest-value answer in the whole interview — every explanation should be framed against a
language the learner already knows ("this is Python's decorator, but checked at compile time"). Never explain a
concept from scratch that they already have under a different name.

## Folder Layout

```
NN-<slug>/
  README.md        the lesson
  exercises/       one file per problem, with the problem statement as a comment header
  solutions/       one file per exercise, idiomatic and commented
```

If `toolchain_setup` is yes, also write at the repo root the language's standard project files — the build manifest,
a `.gitignore` for the language, a formatter or linter config if the language has a standard one, and a `hello`
target that can be run immediately to confirm the toolchain works. Document the exact run command in `README.md`.

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do** — 3–5 concrete capabilities.
2. **The concept** — prose explanation, framed against `prior_languages`.
3. **Worked examples** — runnable code, built up in small steps rather than presented finished.
4. **Where this bites** — the mistakes people actually make with this feature, and the error messages they produce.
5. **Exercises** — pointers into `exercises/`, ordered easy to hard, with the last one open-ended.
6. **Further Reading** — verified links, official docs first.

Every code block must be complete enough to run. No `...` elisions in code the learner is meant to type.
Match the `idiomatic_depth` answer: "read code" stays at recognizing patterns, "production code" covers error
handling, testing, and project structure.
````

- [ ] **Step 2: Write `class-technology.md`**

Same three sections, same structure as the exemplar above. Content:

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What's your current stack? | (free text) | `prior_stack` |
| What are you trying to build or run? | A side project / A production system at work / Evaluating it for a decision / Passing an interview or cert | `use_case` |
| How deep? | Use it confidently / Understand how it works internally / Operate it in production | `depth` |
| Include operations? | Yes — deployment, monitoring, failure modes / No — just development | `ops_scope` |

**Folder Layout** — `NN-<slug>/README.md`, `exercises/`, `solutions/`, plus a repo-root `sandbox/` holding a minimal
runnable setup (compose file, config, or starter project) so the learner can try things immediately.

**Lesson Shape** — same six-part order as the exemplar, with two substitutions: section 3 is **Hands-on** (commands
and config the learner runs against `sandbox/`, with expected output shown) and section 4 is **Failure modes** (what
breaks in production and the symptom it presents). If `ops_scope` is yes, every module ends with an operations note.

- [ ] **Step 3: Verify both files have all three sections**

Run:
```bash
for f in programming-language technology; do
  echo "== $f"
  grep -c '^## Round 2 Questions$\|^## Folder Layout$\|^## Lesson Shape$' \
    plugins/learning/skills/teach-me/references/class-$f.md
done
```
Expected: `3` under each filename.

---

### Task 5: Class files — `academic-subject`, `creative-craft`, `professional-domain`, `generic`

**Files:**
- Create: `plugins/learning/skills/teach-me/references/class-academic-subject.md`
- Create: `plugins/learning/skills/teach-me/references/class-creative-craft.md`
- Create: `plugins/learning/skills/teach-me/references/class-professional-domain.md`
- Create: `plugins/learning/skills/teach-me/references/class-generic.md`

**Interfaces:**
- Consumes: the three-section contract from Task 2 and the exemplar structure written in Task 4 Step 1. Read
  `class-programming-language.md` before writing these — match its heading structure, table format, and level of
  detail exactly.

- [ ] **Step 1: Write `class-academic-subject.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| Is there a specific course or exam? | (free text — course name, textbook, or exam) | `course_context` |
| What math or background do you already have? | (free text) | `background` |
| Proofs and derivations, or results and application? | Proofs and derivations / Results and application / Both | `rigor` |
| Do you need worked problem sets? | Yes, with full solutions / Yes, solutions separate so I can try first / No | `problem_sets` |

**Folder Layout** — `NN-<slug>/README.md`, `notes/` (condensed reference sheets per topic), `problem-sets/`
(numbered problems), and `problem-sets/solutions/` (kept in a separate directory when `problem_sets` is
"solutions separate").

**Lesson Shape** — 1. What you'll be able to do. 2. Intuition first — the idea in plain language before any notation.
3. Formal treatment — definitions, then statements, then derivations if `rigor` includes proofs. 4. Worked examples,
fully shown. 5. Problem set pointers, ordered easy to hard. 6. Further Reading — verified links, plus the standard
textbook for the subject named by title and author. Notation must be introduced explicitly the first time it appears.

- [ ] **Step 2: Write `class-creative-craft.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What do you want to be able to make? | (free text) | `goal_output` |
| Any related craft experience? | (free text) | `background` |
| What equipment do you have? | (free text — instrument, tools, software) | `equipment` |
| Technique drills or projects? | Drills first, projects later / Learn through projects / Mix | `practice_style` |

**Folder Layout** — `NN-<slug>/README.md`, `studies/` (small focused exercises with a stated constraint and what to
look for in the result), `portfolio/` (a directory per larger piece, each with a brief and a self-critique prompt).

**Lesson Shape** — 1. What you'll be able to make. 2. The principle — what it is and why it reads the way it does.
3. Demonstrations — described concretely enough to reproduce without images. 4. Common failures — what a beginner's
attempt looks like and the specific cause. 5. Studies and project pointers. 6. Further Reading, plus named artists,
works, or recordings to study. Every study states what "done" looks like — creative work needs an explicit stopping
condition.

- [ ] **Step 3: Write `class-professional-domain.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What's the goal? | Doing the job now / Moving into the field / Working with people who do it / A certification | `goal` |
| What's your current exposure? | None / Adjacent role / Doing it untrained | `exposure` |
| Which jurisdiction, industry, or company size? | (free text — matters enormously for law, accounting, and regulation) | `context` |
| Certification to target? | (free text, or none) | `certification` |

**Folder Layout** — `NN-<slug>/README.md`, `case-studies/` (realistic scenarios with a decision to make and a
discussion of the tradeoffs), `worksheets/` (templates and checklists the learner fills in — the actual artifacts of
the job).

**Lesson Shape** — 1. What you'll be able to do. 2. The concept and why the field does it this way. 3. How it's
actually practiced, including the shortcuts practitioners take. 4. Where people get it wrong, and the consequences.
5. Case study and worksheet pointers. 6. Further Reading — verified links plus the standard professional references.

If `context` names a jurisdiction, say explicitly that rules vary by jurisdiction and that the material is general
education, not professional advice for a specific situation. Do not give a reader a course of action for their own
legal, tax, or financial matter.

- [ ] **Step 4: Write `class-generic.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What does "good at this" look like to you? | (free text) | `success_criteria` |
| What have you already tried or read? | (free text) | `background` |
| Mostly understanding, or mostly doing? | Understanding / Doing / Both | `orientation` |
| Anything specific you want covered? | (free text) | `must_cover` |

**Folder Layout** — `NN-<slug>/README.md`, `exercises/`, `notes/`.

**Lesson Shape** — the exemplar's six-part order with neutral wording: 1. What you'll be able to do. 2. The concept.
3. Worked examples or demonstrations. 4. Common mistakes. 5. Exercise pointers. 6. Further Reading.

Because there is no class-specific structure to lean on, weight `success_criteria` and `must_cover` heavily — they are
the only strong signal available about what this particular course should contain.

- [ ] **Step 5: Verify all four files have all three sections**

Run:
```bash
for f in academic-subject creative-craft professional-domain generic; do
  n=$(grep -c '^## Round 2 Questions$\|^## Folder Layout$\|^## Lesson Shape$' \
      plugins/learning/skills/teach-me/references/class-$f.md)
  [ "$n" = "3" ] && echo "ok   $f" || echo "BAD  $f ($n sections)"
done
```
Expected: four `ok` lines.

---

### Task 6: Class files — `physical-skill`, `fitness`, `cooking`

**Files:**
- Create: `plugins/learning/skills/teach-me/references/class-physical-skill.md`
- Create: `plugins/learning/skills/teach-me/references/class-fitness.md`
- Create: `plugins/learning/skills/teach-me/references/class-cooking.md`

**Interfaces:**
- Consumes: the three-section contract from Task 2 and the exemplar in `class-programming-language.md`.
- Produces: the `DISCLAIMER.md` requirement that `/teach-me` step 7 refers to for `fitness` and `cooking`.

These three carry real-world safety consequences. The disclaimer and sourcing requirements below are not boilerplate — write them in full.

- [ ] **Step 1: Write `class-physical-skill.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| Where are you now? | Never tried / Beginner / Plateaued at intermediate / Returning after a break | `level` |
| Competing or recreational? | Competing / Recreational but want to be good / Casual | `intent` |
| How often can you practice? | Once a week / 2–3 times / 4+ times | `frequency` |
| What access do you have? | (free text — court, gym, wall, coach, partner) | `access` |

**Folder Layout** — `NN-<slug>/README.md`, `drills/` (one file per drill: setup, reps, what good looks like, common
error), `practice-log/` (a dated template the learner fills in after sessions).

**Lesson Shape** — 1. What you'll be able to do. 2. The technique — described so it can be executed without video,
including body position and sequence. 3. How to tell if you're doing it right, and the self-check that catches the
common error. 4. Drill progression, scaled to `frequency`. 5. How this fits into play or competition. 6. Further
Reading — verified links plus named coaches or instructional sources.

Every drill names its prerequisite drill. Warn where a technique carries injury risk if drilled while fatigued or
without progression, and say what to do instead.

- [ ] **Step 2: Write `class-fitness.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What's the goal? | (free text — a specific event, load, or capacity) | `goal` |
| Current training background? | Untrained / Trained but not for this / Currently training | `training_age` |
| Days per week and session length? | (free text) | `availability` |
| Equipment access? | Full gym / Home basics / Bodyweight only / Outdoors | `equipment` |
| Any injuries or limitations I should route around? | (free text, or none) | `limitations` |

**Folder Layout** — `NN-<slug>/README.md`, `programs/` (week-by-week training blocks as tables), `logs/` (a session
log template). Repo root also gets `DISCLAIMER.md`.

**Lesson Shape** — 1. What you'll be able to do. 2. The training principle and the physiology behind it. 3. The
movements, with setup and execution cues. 4. Programming — how sets, reps, load, and progression are chosen, scaled to
`availability` and `equipment`. 5. Program and log pointers. 6. Further Reading — verified links, preferring
recognized coaching or sports-science sources over blogs.

**Required safety handling:**

- `DISCLAIMER.md` states that the material is general fitness education, that it is not medical or nutritional advice,
  that the learner should consult a physician before starting a program especially with existing conditions or
  injuries, and that pain is a stop signal rather than something to train through.
- If `limitations` names an injury or condition, do **not** write rehabilitation protocols or work around it with a
  prescribed substitute. Say plainly that programming around that specific issue is a job for a physiotherapist or
  physician, and keep the course's general material at a level the learner can adapt with professional guidance.
- Search for current programming and safety guidance rather than writing loading recommendations from memory.
- Never prescribe diets, caloric targets, macronutrient splits, or supplements.

- [ ] **Step 3: Write `class-cooking.md`**

**Round 2 Questions** —

| Question | Options | `answers` key |
|---|---|---|
| What do you want to be able to cook? | (free text) | `goal_output` |
| Current comfort in a kitchen? | Barely cook / Follow recipes fine / Improvise already | `level` |
| Equipment? | (free text — oven, stand mixer, wok, scale, thermometer) | `equipment` |
| Any dietary constraints or allergies? | (free text, or none) | `constraints` |

**Folder Layout** — `NN-<slug>/README.md`, `techniques/` (one file per technique: what it does, how to tell it's
working, how it fails), `recipes/` (one file per recipe, weights first with volumes in parentheses). Repo root also
gets `DISCLAIMER.md`.

**Lesson Shape** — 1. What you'll be able to cook. 2. The technique and what is physically happening to the food.
3. Sensory checkpoints — what to see, hear, smell, and feel at each stage, since timings vary by equipment.
4. Failure modes and how to rescue or avoid them. 5. Recipe pointers that apply the technique. 6. Further Reading —
verified links plus named books or authors.

**Required safety handling:**

- `DISCLAIMER.md` states that food-safety guidance varies by jurisdiction, that the figures given are cited to named
  sources the learner should verify, and that anyone cooking for immunocompromised people, pregnant people, young
  children, or the elderly should follow their local food-safety authority.
- Every temperature, time, or storage figure that affects safety must be searched and cited to a named authority
  (for example the USDA or the UK Food Standards Agency), not written from memory.
- If `constraints` names an allergy, add an explicit cross-contamination note to any recipe involving that allergen.

- [ ] **Step 4: Verify sections and safety content**

Run:
```bash
for f in physical-skill fitness cooking; do
  n=$(grep -c '^## Round 2 Questions$\|^## Folder Layout$\|^## Lesson Shape$' \
      plugins/learning/skills/teach-me/references/class-$f.md)
  [ "$n" = "3" ] && echo "ok   $f" || echo "BAD  $f ($n sections)"
done
grep -l 'DISCLAIMER.md' plugins/learning/skills/teach-me/references/class-fitness.md \
                        plugins/learning/skills/teach-me/references/class-cooking.md
```
Expected: three `ok` lines, then both the fitness and cooking paths listed.

- [ ] **Step 5: Verify all nine class files now exist**

Run: `ls plugins/learning/skills/teach-me/references/ | sort`
Expected: exactly ten files — `topic-classes.md` plus `class-` files for all nine class ids.

---

### Task 7: `/next-lesson` SKILL.md

**Files:**
- Create: `plugins/learning/skills/next-lesson/SKILL.md`

**Interfaces:**
- Consumes: the `.teach-me/course.md` schema defined in Task 2 step 8 — keys `topic`, `slug`, `class`, `answers`, and
  `modules[]` with `id`, `title`, `status`.
- Produces: sets `status: written` on a module; Task 8 reads that value to decide what is quizzable.

- [ ] **Step 1: Write the skill**

Write this exact content to `plugins/learning/skills/next-lesson/SKILL.md`:

````markdown
---
name: next-lesson
description: Write out the next outlined module in a course repo created by teach-me
argument-hint: "[module]"
allowed-tools:

- Read
- Write
- Edit
- Bash
- Glob
- WebSearch
- WebFetch

---

Fill in the next unwritten module of a course repo created by `/teach-me`. Reads the stored interview answers and the
most recently written module, then writes the target module in full — lesson, examples, and practice material —
matching the voice and depth of what came before.

**$ARGUMENTS** optionally names a module: an id (`03-modules`), a number (`3`), or a title fragment (`functors`).
With no argument, the first outlined module is written.

## Steps

1. **Locate the course**: Look for `.teach-me/course.md` in the current directory, then walk up parent directories.
   If none is found, tell the user this is not a `/teach-me` course repo and stop.

    If the file exists but cannot be parsed, rebuild it by scanning module directories — a directory whose `README.md`
    says it has not been written yet is `outlined`, any other is `written` — then show the user the reconstructed
    state before continuing.

2. **Pick the module**: Match `$ARGUMENTS` against module ids, numbers, and titles. With no argument, take the first
   module with `status: outlined`. If every module is already written, say so and stop. If the requested module is
   already written, say so and ask whether to rewrite it before touching anything.

3. **Load context**:
    - The `answers` block from the frontmatter — level, motivation, style, and the class-specific answers all shape
      the writing.
    - The target module's outline stub — its objectives and topic outline are the contract for what to write.
    - The highest-numbered module with `status: written`, read in full. This is the style exemplar: match its section
      order, heading style, code block conventions, and depth. Do not invent a new format.
    - Any earlier module the stub names as a prerequisite, so you can build on its terminology instead of
      re-explaining it.

4. **Ground selectively**: Search the web only if this module covers version-specific behavior, installation or
   tooling, current best practices, or safety-sensitive material. Verify every Further Reading link before writing it.

5. **Write the module**: Replace the stub `README.md` with the full lesson and populate the practice directories that
   the exemplar module uses (`exercises/` and `solutions/`, `drills/`, `recipes/`, `problem-sets/`, and so on —
   whatever the existing modules use). Solutions must actually solve the exercises as written.

6. **Update state**: In `.teach-me/course.md`, set the module's `status` to `written` and rewrite the progress
   checklist in the body to match. Update the module index in the repo's `README.md` if it marks written vs pending.

7. **Report**: Say what was written, how many exercises or practice items it contains, and which module is next.

## Important

- Never rewrite an already-written module without asking first.
- Match the exemplar module's format rather than imposing a new one — consistency across the course matters more than
  any individual improvement.
- Never invent a URL. If a link cannot be verified, name the resource without linking it.
- Never stage, commit, or push.
- Write one module per invocation. If the user wants several, invoke the skill again.
- Respect the safety rules of the course's class: fitness material never prescribes medical, rehabilitative, or
  nutritional treatment, and cooking food-safety figures must be cited to a named authority.
````

- [ ] **Step 2: Verify frontmatter and required sections**

Run:
```bash
head -16 plugins/learning/skills/next-lesson/SKILL.md && \
grep -c '^## Steps$\|^## Important$' plugins/learning/skills/next-lesson/SKILL.md
```
Expected: frontmatter with `name: next-lesson` and `argument-hint: "[module]"`, then `2`.

---

### Task 8: `/quiz-me` SKILL.md

**Files:**
- Create: `plugins/learning/skills/quiz-me/SKILL.md`

**Interfaces:**
- Consumes: `.teach-me/course.md` — `class`, `modules[].status`, `modules[].title`.
- Produces: sets `completed: true` on modules scoring 80%+, and writes `.teach-me/quizzes/<date>-<scope>.md`.

- [ ] **Step 1: Write the skill**

Write this exact content to `plugins/learning/skills/quiz-me/SKILL.md`:

````markdown
---
name: quiz-me
description: Quiz the user on written modules of a course repo and record the results
argument-hint: "[module]"
allowed-tools:

- Read
- Write
- Edit
- Bash
- Glob
- AskUserQuestion

---

Test the user on material already written in a `/teach-me` course repo. Asks questions one at a time, grades each
answer with an explanation rather than a bare verdict, records the session, and marks modules complete on a strong
score.

**$ARGUMENTS** optionally names a module to quiz on. With no argument, the scope is asked.

## Steps

1. **Locate the course**: Look for `.teach-me/course.md` in the current directory, then walk up parent directories.
   If none is found, tell the user this is not a `/teach-me` course repo and stop.

    If the file exists but cannot be parsed, fall back to scanning module directories — a directory whose `README.md`
    says it has not been written yet is `outlined`, any other is `written` — and show the user the reconstructed state
    before continuing.

    Collect the modules with `status: written` — only these are quizzable. If none are written, say so and point at
    `/next-lesson`.

2. **Scope the quiz**: If `$ARGUMENTS` names a module, quiz that module only and skip the scope question. Otherwise
   ask both of these in a single `AskUserQuestion` batch:

    | Question | Options |
    |----------|---------|
    | What should I quiz you on? | The most recent module / All written modules / A specific module |
    | How many questions? | 5 / 10 / 20 |

3. **Read the material**: Read the full `README.md` and practice directories of every module in scope. Questions must
   come from what the course actually says, not from general knowledge about the topic.

4. **Generate the questions** using the mix for the course's `class`:

    | Class | Question types |
    |-------|----------------|
    | `programming-language`, `technology` | recall, predict-the-output, fix-the-bug, "how would you implement" |
    | `academic-subject` | definitions, derivations, applied problems |
    | `cooking`, `physical-skill`, `fitness` | technique recall, decision scenarios, diagnose-the-failure |
    | `creative-craft`, `professional-domain` | concept recall, critique, applied judgment |
    | `generic` | recall, application, comparison |

    Spread questions across the modules in scope rather than clustering on one. Order them easy to hard.

5. **Ask interactively**: One question per message. Wait for the answer. Grade it as correct, partial, or incorrect,
   and on anything less than correct, give the right answer, explain why, and point at the module and section that
   covers it. Never mark an answer wrong without explaining it. Do not reveal upcoming questions.

6. **Record the session**: Write to `.teach-me/quizzes/<YYYY-MM-DD>-<scope>.md`, where `<scope>` is the module id for a
   single-module quiz or `all` for a multi-module quiz. If that file already exists, append `-2`, `-3`, and so on.
   Get the date from `date +%Y-%m-%d` via Bash. Record every question, the user's answer, the grade, the final score,
   and the weak spots identified.

7. **Update state**: For each module in scope, if the user scored 80% or better on the questions drawn from that
   module, set `completed: true` in `.teach-me/course.md` and tick it in the progress checklist. Below 80%, leave it
   and name the sections to revisit.

8. **Report**: Give the score, the strongest and weakest areas, and a concrete next step — revisit a section, or run
   `/next-lesson`.

## Important

- Quiz only modules with `status: written`. Never quiz on outlined stubs.
- Draw questions from the course's own material, so a wrong answer always maps to a section the learner can reread.
- Ask one question per message and wait for the answer. Never dump the whole quiz at once.
- Never reveal the answer before the user has responded.
- Never stage, commit, or push.
- Grading is for the learner's benefit, not a gate — be accurate about what was wrong, without padding or harshness.
````

- [ ] **Step 2: Verify frontmatter and required sections**

Run:
```bash
head -17 plugins/learning/skills/quiz-me/SKILL.md && \
grep -c '^## Steps$\|^## Important$' plugins/learning/skills/quiz-me/SKILL.md
```
Expected: frontmatter with `name: quiz-me` and `argument-hint: "[module]"`, then `2`.

- [ ] **Step 3: Verify the three skills agree on the state file**

Run: `grep -c 'teach-me/course.md' plugins/learning/skills/*/SKILL.md`
Expected: a non-zero count for all three skill files. All three must reference the same path.

---

### Task 9: Register in marketplace.json and README.md

**Files:**
- Modify: `.claude-plugin/marketplace.json`
- Modify: `README.md`

**Interfaces:**
- Consumes: plugin name `learning` and version `1.0.0` from Task 1 — these must match exactly.

- [ ] **Step 1: Add the marketplace entry**

In `.claude-plugin/marketplace.json`, insert this object into the `plugins` array **between** the `hello-world` entry
and the `linear-bug-sweep` entry (alphabetically correct against both neighbors):

```json
    {
      "name": "learning",
      "source": "./plugins/learning",
      "description": "Build a personalized course repo on any subject, write lessons on demand, and quiz yourself",
      "version": "1.0.0",
      "author": { "name": "mattbobambrose" },
      "category": "productivity"
    },
```

- [ ] **Step 2: Verify the JSON still parses and the entry is positioned correctly**

Run:
```bash
python3 -m json.tool .claude-plugin/marketplace.json > /dev/null && echo "JSON OK" && \
python3 -c "import json; print([p['name'] for p in json.load(open('.claude-plugin/marketplace.json'))['plugins']])"
```
Expected: `JSON OK`, then a list with `'learning'` appearing directly between `'hello-world'` and `'linear-bug-sweep'`.

- [ ] **Step 3: Add the README catalog row**

In `README.md`, insert this row into the Plugin Catalog table between the `hello-world` and `linear-bug-sweep` rows:

```
| `learning` | Build a personalized course repo on any subject, write lessons on demand, and quiz yourself | 3 | productivity |
```

- [ ] **Step 4: Add the README per-plugin section**

Insert this section between the `### hello-world` section and the `### linear-bug-sweep` section:

````markdown
### learning

Build a personalized course repo on any subject, write lessons on demand, and quiz yourself.

```
/plugin install learning@mattbobambrose-claude-skills
```

| Skill | Description |
|-------|-------------|
| `/teach-me [topic]` | Build a personalized course repo that teaches a subject from history and fundamentals through advanced application |
| `/next-lesson [module]` | Write out the next outlined module in a course repo created by teach-me |
| `/quiz-me [module]` | Quiz the user on written modules of a course repo and record the results |
````

- [ ] **Step 5: Verify README placement**

Run: `grep -n '^### \|^| `learning`' README.md`
Expected: the `| \`learning\`` catalog row appears before `### code-quality`, and `### learning` appears between
`### hello-world` and `### linear-bug-sweep`.

- [ ] **Step 6: Verify descriptions match across all three files**

Run:
```bash
grep -h 'description' plugins/learning/.claude-plugin/plugin.json
grep -A2 '"name": "learning"' .claude-plugin/marketplace.json | grep description
```
Expected: `plugin.json` says "Build a personalized course repo on any subject, then write lessons and quiz yourself";
`marketplace.json` and the README say "Build a personalized course repo on any subject, write lessons on demand, and
quiz yourself". The marketplace and README strings must be identical to each other.

---

### Task 10: End-to-end verification

**Files:** none — verification only.

- [ ] **Step 1: Confirm the full file inventory**

Run: `find plugins/learning -type f | sort`
Expected: exactly 14 files — `plugin.json`, three `SKILL.md` files, `topic-classes.md`, and nine `class-*.md` files.

- [ ] **Step 2: Confirm every SKILL.md has valid frontmatter**

Run:
```bash
for f in plugins/learning/skills/*/SKILL.md; do
  head -1 "$f" | grep -q '^---$' && grep -q '^name: ' "$f" && grep -q '^description: ' "$f" \
    && echo "ok   $f" || echo "BAD  $f"
done
```
Expected: three `ok` lines.

- [ ] **Step 3: Run the repo's own marketplace validator**

Invoke `/validate-marketplace`.
Expected: no errors reported for the `learning` plugin. Fix anything it flags before continuing.

- [ ] **Step 4: Load the plugin locally**

Run: `claude --plugin-dir ./plugins/learning`
Expected: the session starts and `/teach-me`, `/next-lesson`, and `/quiz-me` appear in the skills list. Exit without
running them.

- [ ] **Step 5: Smoke-test `/teach-me` in a scratch directory**

In a throwaway directory outside this repo, run `/teach-me OCaml` and answer the interview using the spec's driving
example — motivation "for a PL class, but teach it as if I were learning it for a job", level none, semester,
hands-on, prior languages Python and Java.

Verify: the class is guessed as `programming-language`; two question batches are asked, not one or six; the lesson
plan is shown for approval before files are written; `00-context/` and module `01` are written in full; later modules
are stubs; `.teach-me/course.md` exists with the dual motivation preserved verbatim; nothing was committed
(`git log` shows no commits).

Then run `/next-lesson` and confirm it writes module `02` and flips its status. Delete the scratch directory when done.

- [ ] **Step 6: Report results**

Report what passed and what failed. Do not claim the plugin works if any step above failed — say which one and why.

---

### Task 11: Commit (only when the user asks)

Per this repo's `CLAUDE.md`, do not run this task until the user explicitly says to commit.

- [ ] **Step 1: Stage the new and modified files**

```bash
git add plugins/learning \
        .claude-plugin/marketplace.json \
        README.md \
        docs/plans/2026-08-02-teach-me-design.md \
        docs/plans/2026-08-02-teach-me-plan.md
```

- [ ] **Step 2: Commit**

```bash
git commit -m "feat: add learning plugin with teach-me, next-lesson, and quiz-me

/teach-me classifies a topic into one of nine classes, interviews the user
in two batched rounds, and scaffolds a course repo with a full history and
context module plus a fully written first module. Later modules ship as
outlined stubs that /next-lesson fills in on demand, reading stored
interview answers and the previous module as a style exemplar. /quiz-me
tests written modules and records scored sessions."
```
