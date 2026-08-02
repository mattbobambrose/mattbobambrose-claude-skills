# Class: technology

## Round 2 Questions

| Question | Options | `answers` key |
|---|---|---|
| What's your current stack? | (free text) | `prior_stack` |
| What are you trying to build or run? | A side project / A production system at work / Evaluating it for a decision / Passing an interview or cert | `use_case` |
| How deep? | Use it confidently / Understand how it works internally / Operate it in production | `depth` |
| Include operations? | Yes — deployment, monitoring, failure modes / No — just development | `ops_scope` |

`prior_stack` is the highest-value answer in the whole interview — every explanation should be framed against tools
the learner already runs ("this is what nginx does for you, but as a sidecar"). Never explain a concept from scratch
that they already have under a different name.

## Folder Layout

```
NN-<slug>/
  README.md        the lesson
  exercises/       one file per problem, with the problem statement as a comment header
  solutions/       one file per exercise, idiomatic and commented
```

Also write at the repo root a `sandbox/` directory holding a minimal runnable setup for the technology — a compose
file, config, or starter project — so the learner can try things immediately. `sandbox/` is written once, the first
time a module needs it, and reused by every later module rather than rebuilt per module. Document the exact command
that brings it up in `README.md`.

## Lesson Shape

Each module `README.md` runs in this order:

1. **What you'll be able to do** — 3–5 concrete capabilities.
2. **The concept** — prose explanation, framed against `prior_stack`.
3. **Hands-on** — commands and config the learner runs against `sandbox/`, with expected output shown.
4. **Failure modes** — what breaks in production and the symptom it presents.
5. **Exercises** — pointers into `exercises/`, ordered easy to hard, with the last one open-ended.
6. **Further Reading** — verified links, official docs first.

Every command and config block must be complete enough to run as shown against `sandbox/`. No `...` elisions in
config the learner is meant to type. Match the `depth` answer: "use it confidently" stays at the interface the
learner drives directly, "understand how it works internally" opens up the mechanism underneath, "operate it in
production" adds capacity planning, upgrades, and on-call reality. If `ops_scope` is yes, every module ends with an
operations note — what to watch, what to alert on, what breaks first at scale.
