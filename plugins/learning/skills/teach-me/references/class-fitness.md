# Class: fitness

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| What's the goal? | (free text — a specific event, load, or capacity) | `goal` |
| Current training background? | Untrained / Trained but not for this / Currently training | `training_age` |
| Days per week and session length? | (free text) | `availability` |
| Equipment access? | Full gym / Home basics / Bodyweight only / Outdoors | `equipment` |
| Any injuries or limitations I should route around? | (free text, or none) | `limitations` |

## Folder Layout

```
NN-<slug>/
  README.md      the lesson
  programs/       week-by-week training blocks as tables
  logs/           a session log template
```

Also write `DISCLAIMER.md` at the repo root.

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do**.
2. **The training principle** — and the physiology behind it.
3. **The movements** — setup and execution cues.
4. **Programming** — how sets, reps, load, and progression are chosen, scaled to `availability` and `equipment`.
5. **Program and log pointers** — pointers into `programs/` and `logs/`.
6. **Further Reading** — verified links, preferring recognized coaching or sports-science sources over blogs.

### Required safety handling

- `DISCLAIMER.md` states that the material is general fitness education, that it is not medical or nutritional
  advice, that the learner should consult a physician before starting a program especially with existing conditions
  or injuries, and that pain is a stop signal rather than something to train through.
- If `limitations` names an injury or condition, do **not** write rehabilitation protocols or work around it with a
  prescribed substitute. Say plainly that programming around that specific issue is a job for a physiotherapist or
  physician, and keep the course's general material at a level the learner can adapt with professional guidance.
- Search for current programming and safety guidance rather than writing loading recommendations from memory.
- Never prescribe diets, caloric targets, macronutrient splits, or supplements.
