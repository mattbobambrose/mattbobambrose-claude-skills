# Class: professional-domain

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| What's the goal? | Doing the job now / Moving into the field / Working with people who do it / A certification | `goal` |
| What's your current exposure? | None / Adjacent role / Doing it untrained | `exposure` |
| Which jurisdiction, industry, or company size? | (free text — matters enormously for law, accounting, and regulation) | `context` |
| Certification to target? | (free text, or none) | `certification` |

## Folder Layout

```
NN-<slug>/
  README.md       the lesson
  case-studies/    realistic scenarios with a decision to make and a discussion of the tradeoffs
  worksheets/      templates and checklists the learner fills in — the actual artifacts of the job
```

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do**.
2. **The concept** — why the field does it this way.
3. **How it's actually practiced** — including the shortcuts practitioners take.
4. **Where people get it wrong** — and the consequences.
5. **Case study and worksheet pointers** — pointers into `case-studies/` and `worksheets/`.
6. **Further Reading** — verified links plus the standard professional references.

For law, tax, accounting, finance, and regulatory topics, the module must say explicitly that the material is general
education, not professional advice for a specific situation — regardless of what `context` says, including when it is
blank. If `context` names a jurisdiction, the module must also say that rules vary by jurisdiction. It must not give
the reader a course of action for their own legal, tax, or financial matter.

If `certification` names one, align the module list with that certification's published syllabus, so the course covers
its examinable areas in an order the learner can study against.
