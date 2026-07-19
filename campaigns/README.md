# interview-drill campaigns

This directory holds practice data for the `interview-drill` skill: self-driven, no-AI-code coding drills used to rebuild hands-on coding fluency for technical interviews.

**This is personal data, not a normal codebase.** Contents may include job-search context (target companies, job descriptions), self-reported skill gaps, and a graded history of practice code — treat it as such before summarizing, sharing, or acting on it.

## Shape

Each subdirectory is an independent **campaign** — a bounded arc of practice, typically toward one target job:

```
<campaign-name>/
  AGENTS.md        # target job, stack, self-reported starting point
  LEDGER.md        # across-drills tracker: scores, active weaknesses, resolved improvements
  NNN-area-slug/
    problem.md      # the drill's premise, task, acceptance criteria
    grade.md         # the review for that attempt
    app/             # the actual runnable code (node_modules gitignored)
```

**Each campaign subdirectory is its own independent git repository** — not shared with any other campaign, and separate from wherever the `interview-drill` skill itself is installed. This directory (`campaigns/`) is just a plain filesystem container, not a repo itself; it holds sibling checkouts the same way a workspace folder can hold several unrelated projects. Git tags (`NNN-start`, `NNN-grade-0N`) inside each campaign's own repo mark the starting and graded states of each of its drills — `git log`, `git diff <tag> <tag>`, and `git show <tag>:<path>`, run from inside that campaign's directory, are how to inspect any past attempt.

## If you're an agent that landed here without other context

This directory is normally driven by the `interview-drill` skill (Setup/Solo/Grade modes, hint-ladder coaching, rubric-based grading) running from elsewhere — its full protocol isn't included here. If asked to summarize progress, read a campaign's `LEDGER.md` (scannable index) and individual `grade.md` files. Don't start a new drill, grade an attempt, or restructure a `LEDGER.md` without that skill's actual rubric and protocol — reconstructing it from this data alone risks getting the grading/coaching rules wrong.
