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
    before continuing. `answers` and `class` cannot be recovered from the filesystem: ask the user which class the
    course is, and use `generic` if they cannot supply one. If `answers` cannot be recovered, re-ask the round-1
    questions (motivation, current level, time budget, and preferred style). Offer to rewrite `.teach-me/course.md`
    from the reconstructed state, and do so before continuing.

    Collect the modules with `status: written` — only these are quizzable. If none are written, say so and point at
    `/next-lesson`.

2. **Scope the quiz**: If `$ARGUMENTS` names a module, quiz that module only and skip the scope question. Otherwise
   ask both of these in a single `AskUserQuestion` batch:

    | Question | Options |
    |----------|---------|
    | What should I quiz you on? | The most recent module / All written modules / A specific module |
    | How many questions? | 5 / 10 / 20 |

    If the user picks "A specific module", ask which one — listing the written modules — before generating any
    questions.

    Marking a module `completed` in step 7 needs at least three of its questions answered. If the chosen scope and
    length cannot give three questions to every module in scope, say so before generating anything and offer the
    two ways out: raise the question count, or narrow the scope to fewer modules. If the user would rather go ahead
    anyway, do — just tell them which modules will be quizzed but left unmarked.

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
   and name the sections to revisit. Only modules with at least three questions drawn from them are eligible to be
   marked `completed` — with fewer than three, leave the module alone regardless of the score.

8. **Report**: Give the score, the strongest and weakest areas, and a concrete next step — revisit a section, or run
   `/next-lesson`.

## Important

- Quiz only modules with `status: written`. Never quiz on outlined stubs.
- Draw questions from the course's own material, so a wrong answer always maps to a section the learner can reread.
- Ask one question per message and wait for the answer. Never dump the whole quiz at once.
- Never reveal the answer before the user has responded.
- Never stage, commit, or push.
- Grading explanations stay inside what the module says. Never introduce a food-safety figure, a training load, or a
  medical or nutritional claim that is not already written and cited in the course material.
- Grading is for the learner's benefit, not a gate — be accurate about what was wrong, without padding or harshness.
