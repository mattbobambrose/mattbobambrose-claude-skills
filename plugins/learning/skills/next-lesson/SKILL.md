---
name: next-lesson
description: Write out the next outlined module in a course repo created by teach-me
argument-hint: "[module]"
allowed-tools:

- Read
- Write
- Edit
- Bash
- Glob
- WebSearch
- WebFetch

---

Fill in the next unwritten module of a course repo created by `/teach-me`. Reads the stored interview answers and the
most recently written module, then writes the target module in full — lesson, examples, and practice material —
matching the voice and depth of what came before.

**$ARGUMENTS** optionally names a module: an id (`03-modules`), a number (`3`), or a title fragment (`functors`).
With no argument, the first outlined module is written.

## Steps

1. **Locate the course**: Look for `.teach-me/course.md` in the current directory, then walk up parent directories.
   If none is found, tell the user this is not a `/teach-me` course repo and stop.

    If the file exists but cannot be parsed, rebuild it by scanning module directories — a directory whose `README.md`
    says it has not been written yet is `outlined`, any other is `written` — then show the user the reconstructed
    state before continuing.

2. **Pick the module**: Match `$ARGUMENTS` against module ids, numbers, and titles. With no argument, take the first
   module with `status: outlined`. If every module is already written, say so and stop. If the requested module is
   already written, say so and ask whether to rewrite it before touching anything.

3. **Load context**:
    - The `answers` block from the frontmatter — level, motivation, style, and the class-specific answers all shape
      the writing.
    - The target module's outline stub — its objectives and topic outline are the contract for what to write.
    - The highest-numbered module with `status: written`, read in full. This is the style exemplar: match its section
      order, heading style, code block conventions, and depth. Do not invent a new format.
    - Any earlier module the stub names as a prerequisite, so you can build on its terminology instead of
      re-explaining it.

4. **Ground selectively**: Search the web only if this module covers version-specific behavior, installation or
   tooling, current best practices, or safety-sensitive material. Verify every Further Reading link before writing it.

5. **Write the module**: Replace the stub `README.md` with the full lesson and populate the practice directories that
   the exemplar module uses (`exercises/` and `solutions/`, `drills/`, `recipes/`, `problem-sets/`, and so on —
   whatever the existing modules use). Solutions must actually solve the exercises as written.

6. **Update state**: In `.teach-me/course.md`, set the module's `status` to `written` and rewrite the progress
   checklist in the body to match. Update the module index in the repo's `README.md` if it marks written vs pending.

7. **Report**: Say what was written, how many exercises or practice items it contains, and which module is next.

## Important

- Never rewrite an already-written module without asking first.
- Match the exemplar module's format rather than imposing a new one — consistency across the course matters more than
  any individual improvement.
- Never invent a URL. If a link cannot be verified, name the resource without linking it.
- Never stage, commit, or push.
- Write one module per invocation. If the user wants several, invoke the skill again.
- Respect the safety rules of the course's class: fitness material never prescribes medical, rehabilitative, or
  nutritional treatment, and cooking food-safety figures must be cited to a named authority.
