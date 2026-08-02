# Class: generic

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| What does "good at this" look like to you? | (free text) | `success_criteria` |
| What have you already tried or read? | (free text) | `background` |
| Mostly understanding, or mostly doing? | Understanding / Doing / Both | `orientation` |
| Anything specific you want covered? | (free text) | `must_cover` |

## Folder Layout

```
NN-<slug>/
  README.md    the lesson
  exercises/
  notes/
```

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do**.
2. **The concept**.
3. **Worked examples or demonstrations**.
4. **Common mistakes**.
5. **Exercise pointers** — pointers into `exercises/`.
6. **Further Reading**.

Because there is no class-specific structure to lean on, weight `success_criteria` and `must_cover` heavily — they
are the only strong signal available about what this particular course should contain.
