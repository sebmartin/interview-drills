# interview-drills

A workspace for practicing coding interviews, built around a Claude Code skill. It generates a realistic task in a scaffolded project, you write the solution, and it grades you against a rubric afterward. The AI doesn't write code for you; it can answer questions and give hints, and hints cost points.

The repo holds the skill, and it's also where you work: you clone it once, run your sessions from it, and your practice lives inside it under `campaigns/`.

## Getting started

Clone the repo, then open a Claude Code session pointed at it, from a terminal or from an IDE:

```
git clone https://github.com/sebmartin/interview-drills.git
cd interview-drills
claude
```

> [!TIP]
> Use one session per drill. Keep it open from the moment the drill is generated through to the grade, since it's what answers your questions and tracks the hints you used. Then start a fresh session for the next drill. See [Getting good results](#getting-good-results) for why.

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

You get a score per rubric dimension, with the lines that earned it, and a note on what to study next. Nothing is written to disk yet: the scores are a draft, and you're expected to argue with them. If a finding doesn't hold up, say so and it gets re-examined. Once you accept the grade, it writes the review, updates the ledger the next drill is chosen from, and commits and tags the result inside that campaign's own local repo. Nothing is pushed and nothing reaches this repo's history; a campaign has no remote unless you give it one. It grades whatever's currently in your files, so you can keep working and ask again.

## How it works

The skill lives in `.claude/skills/interview-drill/` and ships no example content or stack of its own; every competency map is generated from the job posting you give it.

Your practice lives under `campaigns/`, one independent git repo per campaign. That directory is gitignored here, so nothing about the job you're prepping for lands in this repo's history:

```
campaigns/
  acme/
    AGENTS.md              # target job, stack, toolchain
    CLAUDE.md               # one line, imports AGENTS.md
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

Each drill gets git tags at its starting and graded states, so you can diff or revert against the original code at any point. See [Git in a campaign](#git-in-a-campaign).

Full protocol: [`.claude/skills/interview-drill/SKILL.md`](.claude/skills/interview-drill/SKILL.md).

### Campaigns, and how they stay out of this repo

You never create a campaign by hand. When you paste a job posting, the skill runs a short intake, generates the competency map, and then runs `git init` inside `campaigns/<name>/`, so each campaign is a standalone git repo with its own history, its own tags, and its own working tree. Campaigns share nothing with each other, which is why a stray commit while working one job can't land in another's log.

Three things keep that content out of the public repo:

1. **This repo's `.gitignore` excludes `/campaigns/*`**, with a single exception for `campaigns/README.md`. Nothing else under that directory is visible to git here, so `git add` can't pick it up by accident.
2. **Each campaign being its own repo is a second layer.** Even with the ignore rule gone, git refuses to absorb a nested repository's files; it would record a pointer and warn, rather than committing your drills.
3. **The rule in [`AGENTS.md`](AGENTS.md) covers everything else.** Ignoring files protects file contents, not what a commit message, code comment, doc example, or GitHub issue says. That section spells out what must never appear in this repo or its issues and pull requests: real company names, posting excerpts, anything identifying who a campaign is for. It applies whether the repo is public or private, because visibility is a switch and flipping it exposes the whole history at once.

The practical consequence worth knowing: your campaigns exist only on your machine unless you give them a remote of their own. If you want them backed up, point each campaign repo at a private remote or take periodic `git bundle` snapshots.


## Getting good results

**One session per drill.** Everything the skill needs between drills is on disk: the problem, the review, the ledger with your score history and open weaknesses, and a git tag at the starting commit. A new session reads the ledger and picks up with a summary of where you left off, so there's nothing to carry over in conversation.

Staying in one long session across several drills costs you in two ways. Every turn re-sends the accumulated context, so the token spend grows with the length of the session rather than with the work you're doing. And the more drills sit in one context, the easier it is for details to bleed between them, which shows up as confident claims about code that belongs to a different drill. Start the drill, work it, get the grade, then start fresh.

**Use Opus, with reasoning effort set high.** Pick the model with `/model`; set the effort in `/config`, or put `"effortLevel": "high"` in `~/.claude/settings.json`.

This matters most in the two places the skill does real work. Writing a drill means producing a scaffold that builds, starter code with a genuine gap in it, and acceptance criteria that hold up when you push back on them. Grading means reading your diff rather than the file, verifying claims by running the code instead of asserting them, and noticing when a test passes without exercising the thing it names. Both degrade noticeably with less capable settings, and a bad grade is worse than no grade, since it sends the next drill at the wrong target.

## Git in a campaign

A campaign repo holds every drill for that job in one history, as sibling folders. Two kinds of tag mark the points worth returning to:

- **`NNN-start`** is set when the drill is handed to you, on the commit holding the scaffold and the starter code. It's your anchor for "what have I actually changed".
- **`NNN-grade-0N`** is set on the commit made after each grading pass, numbered from 01. The message carries the score.

Tags carry no campaign name, since a campaign repo only ever holds its own.

```
git diff 003-start                       # everything you've changed, committed or not
git diff 003-start 003-grade-01          # what the graded submission changed
git diff 003-grade-01 003-grade-02       # what moved between two grading passes
git show 003-grade-01:003-slug/src/app.ts   # any file exactly as it was when graded
git log 003-start..HEAD -- 003-slug      # commits for this drill only
```

There's no separate folder holding a saved solution. The tagged commit is the record, which is why anything from a past drill is recoverable with `git show`.

While you're working, commit as much or as little as you like. Nothing gates on it, and grading reads your working tree as it stands, uncommitted changes included, so you never need to commit before asking for a grade.

The commits the skill makes for you are staged by explicit path, never with `git add -A`: the drill's folder at setup, and the drill's folder plus the ledger after grading. If it finds changes outside that path it stops and asks rather than folding them in, so a tagged commit always describes one drill. Corrections made after a grade is tagged are new commits; a graded commit is never amended and a tag is never moved.

## Multiple campaigns

You can prep for more than one job at a time. Ask to list your campaigns, or name the one you want to resume. If you don't say, it checks which one you used last.
