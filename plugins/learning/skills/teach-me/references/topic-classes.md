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
