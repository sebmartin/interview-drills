# interview-drills

A Claude Code skill for practicing coding interviews. It generates a realistic task in a scaffolded project, you write the solution, and it grades you against a rubric afterward. The AI doesn't write code for you; it can answer questions and give hints, and hints cost points.

## Getting started

Clone the repo, then open a Claude Code session pointed at it, from a terminal or from an IDE:

```
git clone https://github.com/sebmartin/interview-drills.git
cd interview-drills
claude
```

> [!TIP]
> Keep that session open for the whole drill. It's what tracks the conversation, answers your questions, and grades you at the end.

The skill loads automatically. Tell it what you're prepping for:

```
> I need to prep for an interview for this job post: [paste the full job description here]
```

It asks a couple of follow-up questions, then builds a competency map from the posting: the areas that job actually tests. This starts a new campaign, which is where everything for that job lives: its drills, its weaknesses, its own git history, kept separate from any other job you're prepping for.

If you don't have a posting, say so and it'll ask what stack to practice instead.

```
> give me a drill
```

It picks the area, based on what the ledger says is weakest or hasn't been covered yet, and scaffolds a project with the task left unfinished. You do the rest. Ask conceptual questions as you go; if you're stuck it'll give you a hint before it gives you the answer.

```
> grade me
```

You get a score per rubric dimension, with the lines that earned it, and a note on what to study next. The result updates the ledger, which is what the next drill is chosen from. It grades whatever's currently in the file, so you can keep working and ask again.

## How it works

This repo is the skill itself. It has no example content or stack of its own; every competency map is generated from the job posting you give it.

Practice work lives under `campaigns/`, one independent git repo per campaign:

```
campaigns/
  acme/
    AGENTS.md              # target job, stack, context
    LEDGER.md               # score history, weaknesses, trends
    competency-map.md        # this campaign's areas, generated from the job posting
    001-data-fetching/
      problem.md                 # the task
      grade.md                    # the review
      src/                         # where you coded
    002-cache-invalidation/
      problem.md
      src/                         # in progress, not graded yet
  another-company/
    AGENTS.md              # a different job, a different stack
    LEDGER.md
    competency-map.md        # e.g. a Go backend role: concurrency, error handling, interfaces
    001-concurrency/
      problem.md
      grade.md
      src/
```

Each drill gets a git tag (`NNN-start`, `NNN-grade-0N`), so you can diff or revert against the starting code at any point.

Full protocol: [`.claude/skills/interview-drill/SKILL.md`](.claude/skills/interview-drill/SKILL.md).

## Multiple campaigns

You can prep for more than one job at a time. Ask to list your campaigns, or name the one you want to resume. If you don't say, it checks which one you used last.
