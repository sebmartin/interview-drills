---
name: interview-drill
description: Self-driven interview coding practice with a hard no-crutch rule. The AI generates a realistic interview-style problem (premise + pre-existing starter code + a task with acceptance criteria and a time box), the human solves it solo with NO AI writing or editing code (a graded, logged hint ladder is the only assistance), then the AI grades the result against a rubric and steers the next drill toward weak areas. Use to rebuild hands-on coding fluency for technical interviews, pairing exercises, or take-homes, especially after a stretch of AI-agent-assisted work. Fully stack-generic — ships no pre-built content for any language or framework; each campaign gets a fresh, job-description-driven competency map (React, Node.js, Go, Rust, Swift, whatever the target role needs) generated at campaign-create time. Standalone — no dependency on any other plugin or thread system. Supports multiple independent practice campaigns (e.g. one per target job), each its own private git repo, kept separate from this generic skill's own history and from every other campaign's.
license: MIT
metadata:
  author: sebastien-martin
  version: "3.0"
  scope: fully generic — any job, any stack; no shipped content, every campaign generates its own competency map
---

# Interview Drill

A repeatable loop to rebuild the muscle of writing code **live and unaided** — the exact thing a pairing exercise or take-home tests, and the exact thing that atrophies when you code through an AI agent. The whole value is the **wall** between the mode where the AI helps and the mode where it does not. If that wall leaks, the drill is worthless.

This skill is **standalone**: it lives at `.claude/skills/interview-drill/` inside a generic, shareable **skill repo** — nothing in that repo's history should ever be personal or sensitive, since it may eventually be shared/pluginized. There is no dependency on any other plugin, thread system, or fixed absolute path.

**One repo per campaign, nested on disk under `campaigns/`, never mixed with the skill or with each other.** The skill repo (rooted wherever `.claude/skills/interview-drill/` lives) `.gitignore`s everything under `campaigns/` except one plain orientation file (`campaigns/README.md`) — it has no idea what's inside any campaign. `campaigns/` itself is **not** a git repo — it's a plain filesystem container, the way a workspace folder can hold several unrelated checkouts side by side. Each subdirectory inside it (`campaigns/acme/`, `campaigns/another-company/`, ...) is its **own independent git repo**, with its own history, its own tags, its own working tree. Campaigns never share a repo with each other — a stray uncommitted change or a WIP commit in one campaign can never land in another's log, because they don't share a `.git/` at all. This is what lets each campaign hold real personal/job-search-sensitive content — and be excluded, backed up, or handled with different care (e.g. never pushed anywhere) independently of every other campaign and of the skill's own shareable history.

Any script or instruction that needs a repo root must be explicit about **which** root: `git rev-parse --show-toplevel` run from inside `.claude/skills/interview-drill/` resolves the **skill repo**; the same command run from inside a specific `campaigns/<name>/` resolves **that campaign's own repo**, unrelated to any other campaign's. Never assume "the repo root" means the same thing across contexts, and never assume there's exactly one "other" repo — there's the skill repo, plus one per campaign.

## The one rule

During **Solo mode**, the AI does not write, edit, complete, or dictate solution code, and does not debug the human's code for it. It may only answer conceptual questions and offer graded, logged hints (see `references/hint-ladder.md`). Everything the human ships, the human typed. No exceptions, no "here, let me just show you."

## The repos

| | Skill repo | A campaign's repo (one per campaign) |
|---|---|---|
| **Root** | wherever `.claude/skills/interview-drill/` lives | `campaigns/<name>/` — its own repo, independent of every other campaign and of the skill repo |
| **Holds** | this skill only, plus `campaigns/README.md` (plain orientation text, not sensitive) | that one campaign's drill history, ledger, and grades |
| **Sensitivity** | generic, shareable, safe to eventually publish/pluginize | private — may contain real job-search/personal content; never assume it's safe to share, and never assume one campaign's sensitivity level tells you anything about another's |
| **Tags** | none | `NNN-start`, `NNN-grade-0N` (no campaign prefix needed — each campaign's tags live only in that campaign's own repo, so they can't collide with any other campaign's) |

`git rev-parse --show-toplevel` resolves whichever repo contains your current working directory — run it from inside a specific `campaigns/<name>/` to get *that* campaign's root, never assume it also tells you anything about a different campaign or about `campaigns/` itself (which isn't a repo at all). Never write a command or instruction that assumes there's only one "other" repo besides the skill's.

## Campaigns

A **campaign** is a named, isolated arc of practice — its own repo, its own `LEDGER.md`, its own `AGENTS.md` context (target job, stack, self-reported starting point), its own sequence of `NNN-area-slug/` drills. Campaigns never share a repo, history, ledger, or drill numbering with each other — not just by convention, but structurally: they're separate git repos, so nothing can interleave.

**One campaign is active per session.** Mirrors how the `ai-workspace` threads plugin tracks "the current thread" — resolve it explicitly, state it out loud, then keep operating on it for the rest of the session without re-asking.

There's no fixed command syntax — the human just asks in plain language and the AI maps it to one of:

- **List** — enumerate `campaigns/*/` (plain directory listing, not a repo query); for each, show its name and a one-line status (last drill id/date, pulled from its `LEDGER.md`).
- **Resume `<name>`** (or no name given — list campaigns and ask) — read `campaigns/<name>/LEDGER.md` and `campaigns/<name>/AGENTS.md`, state **"Working on campaign: `<name>`"**, write `<name>` to `.last-campaign` at the skill repo's root (see Output layout), then operate in whichever of Setup/Solo/Grade the human asks for, scoped to `campaigns/<name>/` and its own repo.
- **Create `<name>`** — run the intake quiz (see `references/campaign-create.md`), `git init` a fresh repo at `campaigns/<name>/`, bootstrap its `LEDGER.md` and `AGENTS.md` (+ `CLAUDE.md` import), then immediately become the active campaign (create implies resume, same as the analogous threads command) — state "Working on campaign: `<name>`", write `.last-campaign`.

**Session-start nudge** (not a hard gate): if a drill-shaped request comes in and no campaign is active yet this session, check `.last-campaign` and ask whether to resume it or pick another, per root `AGENTS.md`. Skip the ask if the human already named a campaign, or if there's only one campaign to begin with.

Setup/Solo/Grade below all assume a campaign is already active — they operate on `campaigns/<active>/`, inside that campaign's own repo, never on the skill repo or any other campaign's repo.

## Roles by mode

| Mode | Human | AI |
|---|---|---|
| **Campaign create** (per new campaign) | Requests it explicitly; answers a short intake quiz | `git init`s a fresh repo at `campaigns/<name>/`; captures target/stack/self-reported weaknesses into its `AGENTS.md` (imported by `CLAUDE.md`); seeds its `LEDGER.md`; becomes active |
| **Setup** | Picks a target area (or asks for weakest); sets up the scaffold with AI help | Generates the problem packet; scaffolds/bootstraps the project; commits + tags the starter state |
| **Solo** | Writes ALL solution code; may ask questions; free to commit WIP | Coaches conceptually; offers graded hints; logs every hint; writes NO solution code |
| **Grade** | Submits; reads feedback; logs lessons | Reviews against the rubric; cites specific lines; names weaknesses; commits + tags the graded state; picks the next target |

---

## Mode 1 — Setup (AI drives)

No `setup.sh` — the AI drives Setup directly with its own file-write and shell tools, in the same turn, so there's no intermediate script to write, run, and verify separately (that extra layer was the actual cause of an early scaffold bug: a shell typo in a script silently produced the wrong starter files, caught only because the human noticed a missing file). Fewer moving parts, verify-as-you-go instead of verify-after.

Requires an active campaign (see above) — everything below happens inside `campaigns/<active>/`.

1. **Choose the area.** The human names one from that campaign's own `competency-map.md` (generated at campaign-create time — see `references/competency-map-guide.md`; this skill ships no area content of its own), or asks the AI to pick the weakest area from `campaigns/<active>/LEDGER.md` cross-referenced with the map — prefer `Status: self-reported` or `open` rows and areas listed as not-yet-covered. If the campaign spans multiple stacks, the chosen area determines which stack this drill uses. Read `campaigns/<active>/AGENTS.md`'s Context section too: weight premise choice toward the target job's domain and any self-reported starting weaknesses.
2. **Pick a drill id.** `NNN-area-slug`, zero-padded, next in sequence — scan existing `campaigns/<active>/NNN-*` folders (numbering is per-campaign, not repo-global).
3. **Scaffold the project directly into `campaigns/<active>/NNN-area-slug/src/`** (help is encouraged here — bootstrapping is not what's tested). The scaffold tool and toolchain depend entirely on this drill's stack, per the competency map — no default (e.g. React+TS via `npm create vite@latest src -- --template react-ts`; other stacks use their own standard scaffold tool). **Don't pre-create the target directory first** — most scaffolders refuse to write into a non-empty directory and cancel silently rather than erroring; let the tool create it. Enable a standing linter/format config as a default for every drill, whatever this stack's equivalent is. (Vite's own template nests a further `src/` inside the project root — that's Vite's convention, not this skill's; the result is `NNN-area-slug/src/src/App.tsx` for a React drill specifically. Don't try to flatten it away.)
4. **Read the scaffolded default files before overwriting them** — scaffold tool defaults drift across versions and tools, don't assume a prior drill's boilerplate. This also shows which default assets the new starter will leave unreferenced.
5. **Write the pre-existing starter code directly into the project** — real files, task genuinely left undone. Write them with the AI's normal file tools, not a heredoc/script. Then delete any scaffold leftovers step 4 surfaced as now-unused — don't hand off a drill with dead default assets already sitting in it.
6. **Generate the problem packet** using `references/problem-template.md`. It must include: a realistic premise, a description of the pre-existing starter code just written (so it feels like inheriting a codebase, not a blank page), a specific task with **acceptance criteria**, an explicit **time box**, and the **areas under test**. Save it to `campaigns/<active>/NNN-area-slug/problem.md`.
7. **Verify before handing off**: install dependencies first if this stack requires a separate step — a missing install makes every check below pass vacuously or fail confusingly, and a human hitting this later reads it as "the scaffold is broken" rather than "dependencies were never installed." Then confirm the starter files contain what was intended (read them back), and this stack's actual build/typecheck/lint commands (check the scaffold's own config for what those actually are — don't assume a specific tool) all pass clean. Clean up any build output this leaves behind.
8. **Commit the starter state (in this campaign's own repo) and tag it `NNN-start`** (full protocol in `references/problem-template.md`'s "Git workflow" section — no campaign prefix needed, since this repo only ever holds one campaign's tags). **Check for out-of-scope changes first** (see "Path-scoped commits" in `references/problem-template.md`'s Git workflow section) — stage and commit only this drill's path, never the whole tree; this campaign's repo can still hold multiple drills' worth of history, so a stray change in a different drill folder is still possible even though cross-campaign interleaving no longer is. This is what lets the human diff/revert against the original code on their own during Solo mode, without asking the AI to reconstruct what changed.
9. **Hand off.** State the task, acceptance criteria, time box, and the bright lines. Then stop and let the human work.

## Mode 2 — Solo (human drives, AI hands off the keyboard)

- The human writes every line of the solution.
- The AI may: explain a concept, compare approaches, clarify requirements, point at official docs, sanity-check a *described* plan in words.
- The AI may NOT: write/paste/complete solution code, give a line-level implementation, read the human's file and fix it, or name the exact fix for their specific bug. When tempted, downgrade to a hint (see below).
- **Graded hint ladder** (full protocol in `references/hint-ladder.md`): L1 nudge → L2 concept → L3 name the API/pattern → L4 worked *analogy* on different code. Never L5 "the actual code." **Log every hint** (level + topic) — hints cost rubric points and are the honest record of how much scaffolding the human still needs.
- **Timing is optional, off by default.** Do not impose a clock or a "start now" ritual. The human codes whenever and signals when they want feedback. Only run a timer if the human explicitly asks to simulate a timed exercise; otherwise ignore the time-box field.
- **The human may commit freely** during this mode (WIP checkpoints, whatever) — the skill does not gate or require commits here. `git diff NNN-start` is always available to the human as their own "what have I changed" anchor.

## Mode 3 — Grade (AI reviews)

When the human says "grade me" (or "done", or the time box expires), grading happens in two phases: **draft and discuss**, then **persist**. Nothing gets written to disk or committed until the human has actually seen the scores and confirmed they're fair — a rushed write-then-defend order defeats the point of a discussion, and a score the human successfully argues down is a different fact than the one first computed, so it must not get persisted before that conversation happens.

### Phase A — Draft and discuss (no files written, nothing committed)

1. Read the human's solution from `campaigns/<active>/NNN-area-slug/src/` — working tree as-is, uncommitted changes included. Do not require a commit before grading.
2. Score against `references/rubric.md` — every dimension gets a score AND a one-line justification citing specific files/lines. Dimensions 2 and 3 are generic slots ("Language & type-system usage", "Framework & domain idioms") — read the active campaign's `competency-map.md` for what they concretely mean for this drill's stack before scoring them. Dimension 7 (Clarity & idiom currency) always runs, unprompted: flag naming/style that would read as dated to a ~5-year unassisted practitioner in this stack (boolean-naming consistency, short-circuit chains vs. ternaries, missed destructuring/shorthand, or the equivalent idiom-currency signal for whatever stack this drill used, etc.), cite the line, name the idiomatic alternative.
3. Draft **learnable** feedback: what was strong, what was weak and *why*, the 2–3 highest-leverage things to study, and any idiom/API they reached for the hint ladder on (those are the study list). On a re-grade, run `git diff NNN-grade-0(N-1) NNN-grade-0N` (once a graded commit exists) or diff against the working tree to ground the process notes in what actually changed, rather than relying on memory of the conversation. If listing commits rather than diffing (e.g. to narrate the attempt's history), path-filter with `-- NNN-area-slug` — a bare tag-range `git log` can include unrelated commits that landed on other drills *within this same campaign* in between (see "Path-scoped commits" below; this can no longer happen across campaigns, since each has its own repo).
4. Factor in hints used and time vs. box.
5. Draft the recommended **next drill's target** (usually the weakest dimension or an uncovered area).
6. **Present the full draft in conversation** — every dimension's score and justification, strengths, weaknesses, study list, next-target — and stop there. Treat every score and claimed bug as falsifiable, not a verdict: invite pushback explicitly (e.g. "let me know if any of this doesn't hold up"). If the human pushes back or asks a question, actually re-examine the code/behavior in question rather than defending the first take by default — a correction that survives scrutiny is worth more than a first draft that doesn't. Revise scores, justifications, or the writeup in place, in conversation, for as many rounds as it takes. Do not write `grade.md`, do not touch `LEDGER.md`, do not commit or tag anything during this phase, no matter how confident the initial draft feels.

### Phase B — Persist (only once the human confirms the grade is fair)

Trigger: the human says something to the effect of the draft being fair/accepted/good to save — not merely the absence of objection to phase A's first message.

7. Save the finalized version to `campaigns/<active>/NNN-area-slug/grade.md`, then update `campaigns/<active>/LEDGER.md` per `references/ledger-template.md`'s "How the AI should use it" section — the ledger tracks weaknesses as status rows tagged with which drills moved them, not growing prose; keep it a scannable index and let `grade.md`/git history hold the detail.
8. **Commit the graded state (in this campaign's own repo) and tag it `NNN-grade-0N`** (N = this grading pass's number, 1-indexed). **Check for out-of-scope changes first** (see "Path-scoped commits" in `references/problem-template.md`'s Git workflow section) — stage and commit only this drill's path, never the whole tree. Commit message includes the score, e.g. `003: attempt 1 — 4.5/5 (decomp/state 5, clarity 4)`. Never amend a prior graded commit; each pass gets its own. There is no separate `solution/` snapshot — the tagged commit itself is the permanent record of that attempt (`git show NNN-grade-0N:NNN-area-slug/src/App.tsx`, run from inside this campaign's repo, retrieves any file from it — note this path is stack-specific; a Vite/React drill's file would actually sit at `NNN-area-slug/src/src/App.tsx` per Vite's own nested convention, see Setup step 3).

If new back-and-forth happens *after* phase B's commit (a later re-examination changes a score), that's a correction, not an amendment: write it as a fresh commit that updates `grade.md`/`LEDGER.md`, same as any other change made after a graded tag already exists. Never amend or move a tag that's already been created.

---

## Progress ledger

`campaigns/<active>/LEDGER.md` is that campaign's across-drills memory: a **Drills** table (one row per drill: id, area, date, time vs. box, hints, per-dimension scores, next target) plus an **Active weaknesses** table (one row per tracked issue, with status and which drills moved it) and an **Improvements** section (resolved weaknesses, kept as short paragraphs for regression-watching). It's a scannable index, not a narrative — full context for any drill lives in that drill's `grade.md` and git history. Read it in Setup to pick the weakest area; update it in Grade per `references/ledger-template.md`'s usage notes. This is what turns isolated reps into a curriculum with spaced repetition on gaps, and what makes a weakness's trajectory (improving/regressing/stuck) visible at a glance instead of buried in prose. Each campaign's ledger is independent — a weakness tracked in one campaign says nothing about another.

## Output layout

```
<skill-repo-root>/                    # THE SKILL REPO — generic, shareable. `git rev-parse --show-toplevel`
                                       # from inside .claude/skills/interview-drill/ resolves here.
  .claude/skills/interview-drill/     # this skill (may instead live in a plugin install, outside any single repo)
  README.md                           # what this repo is
  AGENTS.md                           # active-campaign nudge protocol
  CLAUDE.md                           # just `@AGENTS.md`
  .last-campaign                      # gitignored — plain text, last-active campaign name
  .gitignore                          # node_modules/, dist/, .vite-temp/, .DS_Store, .last-campaign, /campaigns/*
                                       # (with campaigns/README.md un-ignored — see below)
  campaigns/                          # PLAIN DIRECTORY, not a repo — just a container for sibling campaign repos.
                                       # `git rev-parse --show-toplevel` from directly in here fails (no repo).
    README.md                         # tracked BY THE SKILL REPO (generic, safe content) — orientation for
                                       # any agent that lands in this directory without other context
    acme/                          # ITS OWN REPO — `git rev-parse --show-toplevel` from in here resolves to
                                       # campaigns/acme/ itself, unrelated to any other campaign's repo.
      AGENTS.md                       # written/appended by campaign create — target job/stack/self-report
      CLAUDE.md                       # just `@AGENTS.md`
      LEDGER.md                       # this campaign's across-drills tracker — canonical, git-tracked
      competency-map.md               # THIS CAMPAIGN'S areas + rubric-dimension specifics — generated fresh
                                       # from the target job description at create time, never shared/reused
      NNN-area-slug/
        problem.md                    # the generated packet (premise, starter-code notes, task, criteria)
        grade.md                      # the review + lessons
        src/                          # the RUNNABLE project — node_modules/target/etc. gitignored, everything else tracked
    another-company/                             # ANOTHER independent repo — its own .git/, shares nothing with acme/'s
      ...                             # same shape as acme/ above
```

No thread, no second location, no `solution/` snapshot folder. Git tags (`NNN-start`, `NNN-grade-0N`), inside each campaign's own repo, are the historical record for that campaign — `git log`, `git diff <tag> <tag>`, and `git show <tag>:<path>`, run from inside that specific `campaigns/<name>/`, are how you inspect any past state. Never expect a tag or a commit from one campaign's repo to mean anything in another's — they share no history by construction.

**Renaming/copying any of these repos independently** must work with zero changes to any file in this skill, any campaign's `LEDGER.md`, or its `AGENTS.md`: nothing here references any repo's directory name or an absolute path. The repos can be relocated relative to each other too (e.g. `campaigns/` itself, or an individual campaign inside it, moved or symlinked elsewhere) as long as the skill repo's `.claude/skills/interview-drill/` and the `campaigns/<name>/` structure stay intact — but keep it simple and nested unless there's a real reason not to.

## Thread summary (on request only)

If the human is also using the `ai-workspace` threads plugin for a related project (e.g. a job search thread) and asks for one — e.g. "summarize the acme campaign for the thread," or before a specific interview — write a **single summary artifact** into that thread covering that campaign's practice arc so far (drills completed, scores trend, recurring strengths/weaknesses, what's next). This is the *only* point of contact with any thread system, it is not automatic, and it never becomes a second source of truth: the local repo stays canonical always. Do not create or update thread copies of `problem.md`/`grade.md`/`LEDGER.md` — only a standalone summary, written once per request.

## Quick start

```
# List campaigns: "list my campaigns"
# Create a campaign (once per target job/goal): "create a campaign called acme"
#   → AI runs a short quiz (target job? stack? self-reported weak spots?), writes
#     campaigns/acme/AGENTS.md (+ CLAUDE.md import), seeds campaigns/acme/LEDGER.md,
#     becomes the active campaign, then stops.
# Resume a campaign: "resume acme"
#   → states "Working on campaign: acme", reads its LEDGER.md/AGENTS.md.
#     (At session start, if a drill request comes in with no active campaign yet,
#     the AI checks .last-campaign and asks to resume it or pick another.)
# Setup: "give me a drill on data fetching"  (or) "give me a drill" (AI picks the weakest area)
#   → AI scaffolds the project, writes starter code + problem.md directly (under the active
#     campaign), verifies it builds, commits + tags NNN-start in that campaign's own repo,
#     states the task + criteria, then stops.
# Solo:  you code whenever you want. Ask conceptual questions freely; the AI hints, not solves. No clock.
#        Commit WIP whenever you like; `git diff NNN-start` any time to see your full delta.
# Grade: "grade me"  → AI drafts scores vs. the rubric IN CONVERSATION ONLY, invites
#        pushback/questions, revises as needed. Only once you confirm it's fair does
#        it write grade.md, update the active campaign's LEDGER.md, and commit + tag
#        NNN-grade-0N in its own repo.
```

## Notes for the AI running this skill

- Protect the wall. The human's goal is to *not need you* in the room. Solving it for them defeats the entire exercise, even when they're frustrated. Offer the next hint level instead.
- Make starter code realistic: a partial, slightly-imperfect codebase with a couple of existing patterns to follow (and maybe one mild smell), not a pristine blank slate.
- Grade honestly and specifically. Vague praise is a disservice. Cite lines.
- Keep the human's stated preferences in mind (modest tone, honest framing). This skill is generic — do not hard-code any single employer into it.
- Never write an absolute path or a literal repo-directory name into this skill, `problem.md`, `grade.md`, any `LEDGER.md`, or any `AGENTS.md`. Always resolve the relevant repo root via git at the time you need it — and be explicit about *which* repo (the skill's, or a specific campaign's) a given `git` command is meant to operate on; running it from the wrong working directory silently targets the wrong repo instead of erroring.
- Never mix up campaigns: confirm which campaign is active (state it out loud) before any Setup/Solo/Grade action, and never write one campaign's drill under another's `campaigns/<name>/` path. This also means never running a `git` command meant for one campaign's repo while cwd'd into a different campaign's — check `git rev-parse --show-toplevel` if there's any doubt which repo you're actually in.
- Never commit campaign content (drill code, grades, ledgers, `AGENTS.md` context) into the skill repo, never commit skill files into a campaign's repo, and never commit one campaign's content into another campaign's repo. If a `git add`/`git commit` is about to happen, check which repo the current working directory belongs to first.
- Never assume a scaffold's tooling — check `src/package.json`'s `scripts` (or the stack's equivalent manifest) before running lint/build/test by hand (e.g. current Vite templates ship `oxlint`, not `eslint`; running the wrong binary produces a config-not-found error that looks like a broken scaffold but isn't). If a check fails, diagnose (missing `node_modules`? wrong tool invoked?) before telling the human anything is broken.
