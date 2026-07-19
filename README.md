# interview-drills

A technical interview simulator, built as a Claude Code skill.

You get a real coding task, in a real scaffolded project. You write the whole solution. The AI never touches your code, it only answers conceptual questions and gives hints, and hints cost points.

When you're done, it grades you against a rubric. It tracks your weaknesses across drills and designs the next one to target them.

## Getting started

```
git clone https://github.com/sebmartin/interview-drills.git
cd interview-drills
claude
```

The skill loads automatically. Tell it what you're prepping for:

```
> I need to prep for an interview for this job post: [paste the full job description here]
```

It asks a couple of quick questions, then builds a **competency map**: the specific areas this job actually tests, not a generic checklist. This is a new **campaign**, with its own history.

No posting to paste? Say so. It'll ask for a stack instead.

Then:

```
> give me a drill
```

You don't pick the area, it does, whatever the ledger says is weakest or untouched. It scaffolds a real project with the task already broken, and stops.

You fix it. Ask anything conceptual along the way. Get stuck, get a hint, never the answer.

```
> grade me
```

Scored rubric, cited lines, next target. Repeat.

## Examples

A campaign for a Senior Full-Stack posting (React/TS frontend, Node/TS backend) generated this competency map:

| #   | Area                                            | Proves                                                                 |
| --- | ----------------------------------------------- | ---------------------------------------------------------------------- |
| 1   | Component decomposition & props modeling        | Splitting UI into cohesive, well-typed components                      |
| 4   | Data fetching (loading/error/race/cancellation) | Handling the real failure modes of async UI, not just the happy path   |
| 7   | Background job processing                       | Queuing/retrying work correctly, not just calling it inline and hoping |
| 9   | Cache invalidation                              | Knowing when a cached value is stale and what to do about it           |

Four drills in, the ledger already tracks a recurring weakness, a resolved one with a regression note, and a score history. That's what picks drill five, not a coin flip.

A campaign for a backend-heavy Go posting generated a completely different map, same skill:

| #   | Area                            | Proves                                                             |
| --- | -------------------------------- | -------------------------------------------------------------------- |
| 1   | Concurrency (goroutines/channels) | Coordinating concurrent work correctly, no races, no leaked goroutines |
| 3   | Error handling & wrapping         | Errors carry enough context to debug, without panicking on the happy path |
| 5   | Interface design                  | Small, composable interfaces, not a god-interface with ten methods    |
| 8   | Graceful shutdown                 | In-flight work finishes cleanly on signal, nothing silently dropped |

No shared content between these two. Each map comes from its own job posting.

## How it works

This repo is the skill: generic, stack-agnostic, no shipped example content.

All real practice work lives under `campaigns/`. Each campaign is its own git repo. Nothing shared, nothing that leaves your machine.

```
campaigns/
  acme/
    AGENTS.md            # target job, stack, context
    LEDGER.md             # score history, weaknesses, trends
    competency-map.md      # this campaign's focus areas
    001-data-fetching/
      problem.md             # the task
      grade.md                # the review
      src/                     # where you coded
    002-cache-invalidation/
      problem.md
      src/                     # in progress, not graded yet
  another-company/
    AGENTS.md            # different job, different stack
    LEDGER.md
    competency-map.md      # Go, not React. Completely different areas.
    001-concurrency/
      problem.md
      grade.md
      src/
```

Every drill is git-tagged (`NNN-start`, `NNN-grade-0N`), so you can diff or revert against the starting code anytime.

Full protocol: [`.claude/skills/interview-drill/SKILL.md`](.claude/skills/interview-drill/SKILL.md).

## Multiple campaigns

Nothing stops you targeting several jobs at once. Ask to list them, or name one to resume. If you don't say, it checks which one you used last.
