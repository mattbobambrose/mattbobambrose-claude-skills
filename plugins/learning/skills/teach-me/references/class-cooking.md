# Class: cooking

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| What do you want to be able to cook? | (free text) | `goal_output` |
| Current comfort in a kitchen? | Barely cook / Follow recipes fine / Improvise already | `level` |
| Equipment? | (free text — oven, stand mixer, wok, scale, thermometer) | `equipment` |
| Any dietary constraints or allergies? | (free text, or none) | `constraints` |

## Folder Layout

```
NN-<slug>/
  README.md        the lesson
  techniques/       one file per technique: what it does, how to tell it's working, how it fails
  recipes/          one file per recipe, weights first with volumes in parentheses
```

Also write `DISCLAIMER.md` at the repo root.

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to cook**.
2. **The technique** — what is physically happening to the food.
3. **Sensory checkpoints** — what to see, hear, smell, and feel at each stage, since timings vary by equipment.
4. **Failure modes** — how to rescue or avoid them.
5. **Recipe pointers** — pointers into `recipes/` that apply the technique.
6. **Further Reading** — verified links plus named books or authors.

### Required safety handling

- `DISCLAIMER.md` states that food-safety guidance varies by jurisdiction, that the figures given are cited to named
  sources the learner should verify, and that anyone cooking for immunocompromised people, pregnant people, young
  children, or the elderly should follow their local food-safety authority.
- Every temperature, time, or storage figure that affects safety must be searched and cited to a named authority (for
  example the USDA or the UK Food Standards Agency), not written from memory.
- If `constraints` names an allergy, add an explicit cross-contamination note to any recipe involving that allergen.
