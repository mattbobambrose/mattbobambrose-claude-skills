# Class: physical-skill

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| Where are you now? | Never tried / Beginner / Plateaued at intermediate / Returning after a break | `level` |
| Competing or recreational? | Competing / Recreational but want to be good / Casual | `intent` |
| How often can you practice? | Once a week / 2–3 times / 4+ times | `frequency` |
| What access do you have? | (free text — court, gym, wall, coach, partner) | `access` |

## Folder Layout

```
NN-<slug>/
  README.md        the lesson
  drills/           one file per drill: setup, reps, what good looks like, common error
  practice-log/     a dated template the learner fills in after sessions
```

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do**.
2. **The technique** — described so it can be executed without video, including body position and sequence.
3. **How to tell if you're doing it right** — the self-check that catches the common error.
4. **Drill progression** — pointers into `drills/`, scaled to `frequency`.
5. **How this fits into play or competition**.
6. **Further Reading** — verified links plus named coaches or instructional sources.

Every drill names its prerequisite drill. Warn where a technique carries injury risk if drilled while fatigued or
without progression, and say what to do instead.
