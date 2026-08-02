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

## Grounding

**Ground the material selectively**: Use WebSearch and WebFetch for the content most likely to be wrong or stale —
`00-context` dates, creators, and adoption claims; install steps and current stable versions; current best
practices; and the safety-sensitive content called out in the `fitness`, `cooking`, and `physical-skill` class
files. Core concepts, worked examples, and exercises come from your own knowledge. Every Further Reading link must
be fetched and confirmed to exist before you write it down.

## Steps

1. **Determine the topic**: Use `$ARGUMENTS`. If empty, ask what the user wants to learn and wait for an answer.

2. **Classify the topic**: Read `${CLAUDE_PLUGIN_ROOT}/skills/teach-me/references/topic-classes.md` and pick the
   best-matching class. State the guess in one line and invite correction, e.g. "Treating *bread baking* as a
   **cooking** topic — say so if you'd rather it be something else." Then read the matching
   `${CLAUDE_PLUGIN_ROOT}/skills/teach-me/references/class-<class>.md` file in full. These reference files live in
   this skill's own directory, not in the course directory being written into. Do not proceed until the class is
   settled.

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
    <class root files>     from the class file's Folder Layout — toolchain files, sandbox/, DISCLAIMER.md
    00-context/README.md   FULL
    01-<slug>/             FULL — README.md plus the class's practice directories
    02-<slug>/README.md    OUTLINED
    ...
    NN-capstone/README.md  OUTLINED
    ```

    - Before writing any files, check for a git repository with `git rev-parse --git-dir`. If there is none, run
      `git init` in the target directory. Never stage, commit, or push.
    - Do the grounding searches described in the **Grounding** section before writing each section, not after.
    - Class-specific root files come from the class file's **Folder Layout** section. The cases are: the toolchain
      files `class-programming-language.md` requires when `toolchain_setup` is yes (build manifest, a `.gitignore`
      for the language, a formatter or linter config, and a runnable `hello` target), the `sandbox/` directory
      `class-technology.md` requires unconditionally, and `DISCLAIMER.md`.
    - `00-context/README.md` is always written in full and always covers: origin and history, who created it and why,
      what problems it solves, where it is used today, the surrounding ecosystem, honest tradeoffs and criticisms, and
      how it compares to its main alternatives.
    - Module `01` is written in full, following the section order given in the class file's **Lesson Shape** section:
      lesson prose, worked examples, and the practice directories named in the class file's **Folder Layout** section,
      populated with real content.
    - Every other module gets a stub `README.md` containing: title, learning objectives, a topic outline,
      prerequisites (which module comes first), estimated time, and a closing line stating it has not been written yet
      and that `/next-lesson` will fill it in.
    - Classes `fitness`, `cooking`, and `physical-skill` also get a `DISCLAIMER.md` at the repo root, per their class
      file.

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
      toolchain_setup: true
      idiomatic_depth: production
    modules:
      - {id: "00-context", title: "History and Context", status: written}
      - {id: "01-basics",  title: "Basics",              status: written}
      - {id: "02-syntax",  title: "Syntax and Types",    status: outlined}
    ---

    ## Progress

    - [ ] 00 History and Context
    - [ ] 01 Basics
    - [ ] 02 Syntax and Types
    ```

    - `status` is `written` or `outlined`. `completed: true` is added later by `/quiz-me` or by the user.
    - Record every round-1 and round-2 answer under `answers`, using the class file's key names. The round-1 keys are
      `motivation`, `level`, `time_budget`, and `style`, and class-file keys must never reuse them.
    - Get the date from `date +%Y-%m-%d` via Bash — never guess it.

9. **Report**: State what was created and where, which modules are written vs outlined, and that `/next-lesson`
   writes the next module and `/quiz-me` tests what exists.

## Important

- Never overwrite an existing file without explicit confirmation.
- Never stage, commit, or push. `git init` is the only permitted git write operation, and only when no repository
  exists.
- Never invent a URL. If a link cannot be verified, describe the resource by name instead of linking it.
- Do not write later modules in full. Outlined stubs are deliberate — they let `/next-lesson` adapt to the learner's
  actual progress.
- Get approval on the lesson plan in step 6 before writing any course files.
- `fitness`, `cooking`, and `physical-skill` courses must include `DISCLAIMER.md`. Fitness material stays at the level
  of general training principles and never prescribes medical, rehabilitative, or nutritional treatment. Cooking
  food-safety figures must be cited to a named authoritative source.
