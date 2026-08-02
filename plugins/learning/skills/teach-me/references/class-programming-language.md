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
