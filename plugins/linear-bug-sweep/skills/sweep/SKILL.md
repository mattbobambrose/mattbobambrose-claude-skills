---
name: sweep
description: >
  Systematically work through Linear issues from a project one by one — verifying, fixing, or closing each with
  build verification and status updates. Use when the user wants to "go through bugs", "work through Linear issues",
  "sweep bugs", "fix Linear issues one by one", "triage issues", or resolve a backlog of issues from a Linear project.
  Also trigger when the user asks to "resolve non-idiomatic code", "clean up code issues", or "work through tech debt"
  and has Linear issues tracking them.
---

# Linear Bug Sweep

Work through a Linear project's Todo issues one by one: verify each against the codebase, fix or close it, confirm
the build, update Linear, and move to the next.

## Setup

1. Use `list_teams` and `list_projects` to discover available teams and projects. If multiple projects exist, ask the
   user which one to sweep. If only one exists, auto-select it and tell the user.

2. Use `list_issue_statuses` with the selected team to find the "Todo" state ID.

3. Fetch Todo issues in batches of 10 using `list_issues` with the selected project and Todo state. For each issue,
   extract only:
   - `identifier` (e.g., "TEAM-42")
   - `title`
   - `priority.name`
   - `assignee`
   - `labels` (exclude the "AI" label)

   Page through using the `cursor` until all issues are fetched.

4. Sort by priority (1=Urgent first, 2=High, 3=Medium, 4=Low, 0=No priority last) and present as a table:

   | ID | Priority | Title | Labels | Assignee |
   |----|----------|-------|--------|----------|

5. If there are no Todo issues, tell the user and stop.

6. Confirm with the user: "Ready to start from the top?" before proceeding.

## Per-Issue Loop

For each issue, follow this sequence:

### 1. Present the Issue

Format each issue header like this — always include the file path and line numbers so the user can orient themselves
immediately:

```
**TEAM-XX (Priority): Title**

**File:** `path/to/file.ext:line-numbers`
```

Read the issue description to understand what file(s) and lines are affected. Then read the referenced file(s) to
understand the current state of the code.

### 2. Check If Already Resolved

Before proposing any fix, verify the issue still exists in the codebase. Search for the specific pattern described
in the issue (the function name, the anti-pattern, the problematic line). If the code has already been fixed or the
file/route/class no longer exists:

- Mark the issue as **Done** in Linear using `save_issue`
- Update the description with what was found (e.g., "Already resolved — the endpoint was removed in commit X")
- Tell the user it was already fixed and move immediately to the next issue

### 3. Explain and Propose

If the issue is still present:

- Briefly explain the problem and its impact
- Propose a specific fix with the approach and any trade-offs
- If there are meaningful design choices (e.g., multiple valid approaches, tension between simplicity and type safety),
  present the options and let the user decide
- For trivial fixes, state what you'll do and ask "Want me to apply this, or would you like to?"

### 4. Apply the Fix

After agreement:

- Make the code change
- Run the project's build command (e.g., `./gradlew build -x test`, `npm run build`, `cargo check`) to verify
  compilation and linting pass
- If lint or build errors result from the change, fix them iteratively until the build passes
- For changes touching logic (not just style), run relevant tests too

### 5. Update Linear

After each issue is resolved (whether fixed, already resolved, or won't fix), use `save_issue` to update it:

- Set `state` to **Done** or **Canceled** as appropriate
- Update the `description` with two sections:

```markdown
## Description
[1-2 sentence summary of the original problem]

## Resolution
[What was done to fix it, or why it was closed as won't fix]
```

For won't fix issues, use `## Resolution — Won't Fix` as the heading.

### 6. Advance

After updating Linear, present the next issue. Keep momentum — don't ask "ready for the next one?" every time
unless the previous fix was complex or the user seems to want a pause.

## Handling Special Cases

**Won't Fix** — If a reported issue turns out to be intentional design (e.g., duplication exists for type safety
reasons, a "redundant" qualifier is actually needed for disambiguation), explain why to the user and recommend
closing as Canceled. Update the description with the rationale so the reasoning is preserved.

**In Progress** — If an issue requires a larger refactor that shouldn't be done in this sweep, mark it as
In Progress and add notes about what was found and what needs to happen. Tell the user why you're deferring it.

**Overlapping Issues** — If fixing one issue also resolves another (e.g., removing dead code fixes both the
"commented-out code" and "hardcoded path" issues), close both and note the connection in each issue's description.

**User Disagrees** — If the user disagrees with a proposed fix or wants to revert a change, do it immediately
and update the Linear issue accordingly (Canceled with explanation, or In Progress with notes).

**Adjacent Fixes** — If two issues affect the same file on adjacent lines, propose fixing both together to
avoid redundant file reads and build cycles. Call out that you're combining them.

## After All Issues

When every issue has been addressed:

1. Provide a summary table:

   | Status | Count | Issues |
   |--------|-------|--------|
   | Done (fixed) | N | TEAM-XX, TEAM-YY |
   | Done (already resolved) | N | TEAM-ZZ |
   | Canceled (won't fix) | N | TEAM-AA |
   | In Progress | N | TEAM-BB |

2. Ask: "Would you like me to update project documentation (README, CLAUDE.md, llms.txt, etc.) to reflect these
   changes?"

3. If the user agrees, identify what changed (renamed files, new utilities, removed endpoints, new conventions) and
   update the relevant docs.

4. After docs are updated (or skipped), ask: "Want me to commit and push?"

## Important

- Always verify the issue still exists before proposing a fix. Many issues may have been resolved by prior work.
- Make minimal, focused changes. Do not refactor surrounding code or add improvements beyond what the issue describes.
- Every code change must be verified by a successful build before moving on.
- Keep Linear descriptions concise but informative — someone reading the issue later should understand what happened
  without needing to look at the code.
