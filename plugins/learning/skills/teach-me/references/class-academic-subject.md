# Class: academic-subject

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| Is there a specific course or exam? | (free text — course name, textbook, or exam) | `course_context` |
| What math or background do you already have? | (free text) | `background` |
| Proofs and derivations, or results and application? | Proofs and derivations / Results and application / Both | `rigor` |
| Do you need worked problem sets? | Yes, with full solutions / Yes, solutions separate so I can try first / No | `problem_sets` |

## Folder Layout

```
NN-<slug>/
  README.md                the lesson
  notes/                    condensed reference sheets per topic
  problem-sets/             numbered problems
  problem-sets/solutions/   kept in a separate directory when `problem_sets` is "solutions separate"
```

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do**.
2. **Intuition first** — the idea in plain language, before any notation.
3. **Formal treatment** — definitions, then statements, then derivations if `rigor` includes proofs.
4. **Worked examples** — fully shown.
5. **Problem set pointers** — pointers into `problem-sets/`, ordered easy to hard.
6. **Further Reading** — verified links, plus the standard textbook for the subject named by title and author.

Notation must be introduced explicitly the first time it appears.
