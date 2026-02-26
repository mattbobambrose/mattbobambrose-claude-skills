---
name: summarize-progress
description: Summarize what the user accomplished over a time period based on git history and provide motivational
messaging
argument-hint: "[today | yesterday | last week | last month | date]"
allowed-tools:

- Bash
- Read
- Grep

---

Summarize the user's accomplishments over a time period by analyzing git commit history, then close with inspirational
or reassuring messaging calibrated to how much they got done.

## Steps

1. **Determine the time range**: If `$ARGUMENTS` is empty, default to today (midnight to now). Otherwise parse the
   argument:
    - `today` — from midnight today to now
    - `yesterday` — the previous calendar day
    - `last week` — the last 7 calendar days
    - `last month` — the last 30 calendar days
    - A specific date like `2025-01-15` — that single calendar day
    - A relative phrase like `3 days ago` or `last friday` — resolve to the correct date using
      `date -d` (Linux) or `date -v` (macOS)

   Compute the `--after` and `--before` timestamps for the resolved range.

2. **Collect commits**: Run `git log --after="<start>" --before="<end>" --all --oneline --author` scoped to the current
   git user (`git config user.name` or `git config user.email`). If the repository has no commits for the target
   period, say so and skip to the messaging step.

3. **Gather details**: For each commit, run `git show --stat <hash>` to capture the files changed, insertions, and
   deletions. Aggregate totals for: number of commits, files changed, lines added, and lines removed.

4. **Summarize accomplishments**: Present a concise summary:
    - **Period**: the time range being summarized (e.g., "Tuesday, Jan 14" or "Jan 8 – Jan 14")
    - **Commits**: total count
    - **Files changed**: total unique files
    - **Lines**: `+<added>  -<removed>`
    - **Highlights**: a bulleted list of the most meaningful changes, grouped by theme or feature area. For single-day
      summaries, aim for 3–7 items. For multi-day ranges, aim for 5–10. Derive these from commit messages and diff
      stats — focus on *what was accomplished*, not raw file names.
    - For multi-day ranges, optionally include a **Day-by-day breakdown** showing commit counts per day if it reveals
      interesting patterns (e.g., a burst of activity on one day).

5. **Closing message**: End with a short motivational paragraph (2-4 sentences) based on the period's output:
    - **High activity** (many commits, large diffs, broad file coverage): celebrate the productivity — acknowledge the
      hard work, highlight the breadth of impact, and encourage keeping the momentum.
    - **Moderate activity**: affirm steady progress — note that consistent work compounds over time and every commit
      moves the project forward.
    - **Low activity** (one or two small commits): be genuinely reassuring — remind the user that some days are for
      thinking, planning, or recharging, and that showing up at all matters. Never be condescending.
    - **No activity**: be warm and supportive — everyone needs rest days, and stepping away often leads to better ideas
      tomorrow.

## Important

- Only summarize commits authored by the current git user — do not include other contributors.
- Do not make any code changes — this skill is read-only.
- Keep the highlights concise; summarize related commits together rather than listing every commit individually.
- The closing message should feel genuine, not generic. Reference specifics from the period's work when possible.
- Never be sarcastic or judgmental about low-activity periods.
