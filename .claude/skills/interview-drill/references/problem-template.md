# Problem Packet Template

Fill this in during Setup and save to `NNN-area-slug/problem.md`, relative to the **active campaign's own repo root** (find it with `git rev-parse --show-toplevel` run from inside `campaigns/<active>/` — never hardcode a path; any repo here may be renamed or moved). Make it feel like inheriting a real, mid-flight codebase, not a blank exercise.

```markdown
# Drill NNN — <short title>

- **Date:** <YYYY-MM-DD>
- **Areas under test:** <from this campaign's competency-map.md, e.g. #4 Data fetching + #5 Lists>
- **Difficulty:** <easy | med | hard>
- **Time box:** <e.g. 60 min>

## Premise
<2–4 sentences of realistic context. A product, a team, a reason this task exists.
 Give it a domain flavor (optionally the target job's domain) so it reads like a real ticket.>

## Pre-existing code
<What the human is handed. Describe the starter code that has ALREADY been written into
 src/: existing components, types, a partial feature, existing patterns to follow,
 and (optionally) one mild smell or gap. The human should open the repo and read before writing.>

## Task
<The specific thing to build/fix/extend. Concrete and bounded to the time box.>

## Acceptance criteria
- [ ] <criterion 1 — observable behavior>
- [ ] <criterion 2>
- [ ] <criterion 3>
- [ ] <edge case that must be handled>

## Explicitly out of scope
<Trim scope so the box is realistic: styling polish, auth, persistence, etc. — say what NOT to build.>

## Stretch (only if time remains)
<An optional harder extension that probes depth.>

## Bright lines (read to the human at handoff)
- You write all solution code. I coach and hint (graded, logged), I don't type it.
- Ask me anything conceptual. When you're stuck, I climb the hint ladder, not to the answer.
- Timer starts now. Say "done" when you're ready to be graded, or when the box expires.
```

## Setup checklist for the AI

Everything in this checklist happens inside the **active campaign's own repo** — `campaigns/<active>/`, independent of the skill's own repo and of every other campaign's (see `SKILL.md`'s "The repos" section). `git rev-parse --show-toplevel` run from anywhere under `campaigns/<active>/` resolves to that campaign's repo root, never a hardcoded path. It's the **sole canonical record** for that campaign's drill content — there is no thread/second-location mirroring, and nothing here is shared with any other campaign. Everything a drill produces (problem, starter code, solved code, grade) lives in this repo's git history.

This checklist assumes a campaign is already active (resumed or just created — see `SKILL.md`'s Campaigns section). Everything below happens inside `campaigns/<active>/`, in that campaign's own repo.

No `setup.sh` or other intermediate script — the AI performs every step below directly with its own tools, in-turn, and verifies as it goes. (An earlier version of this skill used a generated shell script for scaffolding; a shell bug in one silently produced the wrong starter files, caught only because the human noticed a missing file. Direct writes plus immediate verification remove that whole failure class.)

1. **Confirm the active campaign, and read its `competency-map.md`.** Don't guess or default silently — the campaign should already be resolved per `SKILL.md`'s Campaigns section before Setup starts. Pick the target area from that campaign's map (not a shipped catalog — this skill has none); if the campaign spans multiple stacks, the chosen area determines which stack this drill uses.
2. **Pick a drill id.** `NNN-area-slug`, zero-padded, next in sequence — scan existing `NNN-*` folders (or read `LEDGER.md`) at that campaign's repo root to find the next number. Numbering is per-campaign — a fresh campaign starts back at `001` even if other campaigns are well past it, since numbering has never been shared across campaigns even before each got its own repo.
3. **Scaffold the project directly into `NNN-area-slug/src/`** (path relative to the active campaign's own repo root — i.e. `campaigns/<active>/NNN-area-slug/src/` from the skill repo's perspective). The scaffold command and toolchain depend entirely on the drill's stack (from the competency map) — there is no default. **Whatever the stack, do not pre-create the target directory before running its scaffold tool** — most scaffolders (Vite included) refuse to write into a non-empty directory and cancel silently rather than erroring loudly; let the tool create it. `src/`'s build artifacts and dependency directories (`node_modules/`, Go's module cache is fine to leave global, Rust's `target/`, etc.) stay gitignored — only real source lives in this repo's history.
   - *Worked example (React + TypeScript, Vite):* `npm create vite@latest src -- --template react-ts`, then `npm install`. Enable `eqeqeq: "error"` in the scaffolded linter config (`.oxlintrc.json`'s `rules`, or the ESLint equivalent) as a standing default. **Note:** Vite's own template nests a further `src/` inside the project root (its own convention, not this skill's) — the result is `NNN-area-slug/src/src/App.tsx`. Don't try to flatten it away.
   - *Other stacks:* use that ecosystem's own standard scaffold tool and idiomatic starting config (e.g. `go mod init` + a minimal `main.go`, `cargo new`, `swift package init`) — apply the same principle (a real, buildable project, standing linter/format config enabled) rather than copying the Vite specifics literally.
4. **Read the scaffolded default files (and any other file you're about to replace) before overwriting them.** Scaffold tool defaults drift across versions and across tools — don't assume a prior drill's boilerplate, even within the same stack. This also surfaces exactly which default assets the new starter code will leave unreferenced.
5. **Write the pre-existing starter files directly into the project** — real files, task genuinely left undone. Then **delete any now-unused scaffold leftovers** that step 4 surfaced — leftover default assets are the same category of pre-handoff debt as the dead-code cleanup issue tracked for Solo submissions in the campaign's `LEDGER.md`; don't hand off a drill with it already present.
6. **Save `problem.md`** to `NNN-area-slug/problem.md`, describing the starter code just written.
7. **Verify before handing off**: install dependencies first if the stack requires a separate step (e.g. `npm install`) — a missing dependency install makes every check below pass vacuously or fail confusingly, and a human hitting this later reads it as "the scaffold is broken" rather than "dependencies were never installed." Then confirm the starter files contain what was intended (read them back), and this stack's actual build/typecheck/lint commands all pass clean — **check the scaffold's own config (e.g. `package.json`'s `scripts`, a `Makefile`, `cargo.toml`) for what those commands actually are; don't assume** (e.g. current Vite templates ship `oxlint`, not `eslint` — running the wrong linter binary produces a config-not-found error that looks like a broken scaffold but isn't). Clean up any build output this verification leaves behind.
8. **Commit the starter state (in this campaign's own repo) and tag it.** From inside `campaigns/<active>/`: `git add NNN-area-slug/`, commit (e.g. `NNN: starter — <area>`), then `git tag NNN-start` — no campaign prefix needed, this repo only ever holds this one campaign's tags. This is what lets the human diff/revert against the original code on their own during Solo mode — see "Git workflow" below.
9. **Hand off.** State the task, acceptance criteria, time box, and the bright lines. Then stop and let the human work.

## Git workflow

Each campaign is its **own independent git repo** (see `SKILL.md`'s "The repos" section) — `campaigns/<active>/` covers every drill *within that one campaign* as folders in the same history, not a repo per drill, and shares nothing with any other campaign's repo or the skill repo's own history. `src/node_modules` and build output stay gitignored per drill.

**Why one repo per campaign:** the human needs to diff/revert against the exact starter code without asking the AI to reconstruct what changed, and needs that history to stay meaningful as *one campaign's* story — not interleaved with an unrelated campaign's commits just because both happened to share a repo. Git tags anchor the comparison points independent of however many work-in-progress commits the human makes in between; giving each campaign its own repo means those tags can never collide with, or be confused for, another campaign's.

**Tags need no campaign prefix** — each campaign's repo only ever holds that one campaign's tags, so there's nothing to collide with:
- **`NNN-start`** — set immediately after committing the starter scaffold (Setup step 8). `git diff NNN-start` (against the working tree) or `git diff NNN-start HEAD` is the human's "what have I changed" anchor at any point, regardless of how many WIP commits happened in between.
- **`NNN-grade-01`, `NNN-grade-02`, ...`** — set by the AI at the commit made right after each grading pass (see Mode 3 in SKILL.md). `git diff NNN-grade-01 NNN-grade-02` shows exactly what changed between rounds — feed this into `grade.md`'s process notes on re-grades instead of re-deriving it from memory. The tagged commit **is** the solution record — there is no separate `solution/` snapshot folder; `git show NNN-grade-0N:NNN-area-slug/src/App.tsx` (path relative to this campaign's own repo root, stack-specific — a Vite/React drill's file actually sits at `NNN-area-slug/src/src/App.tsx` per Vite's own nested convention; or checking out the tag) retrieves any file at any graded point in history.

**Commit discipline:**
- The human may commit freely at any point while working (WIP checkpoints, exploring an approach, whatever) — the skill does not gate or require commits during Solo mode.
- Grading itself happens against the **working tree as it stands** when the human says "grade me" (uncommitted changes included) — do not require a commit before grading.
- **After** a grading pass completes, commit the graded state with the score in the message (e.g. `003: attempt 1 — 4.5/5 (decomp/state 5, clarity 4)`) and tag it `NNN-grade-0N`. Each grading pass gets its **own new commit** (never amend/squash a prior graded commit) — the log should read starter → attempt 1 → attempt 2 → ... as real, inspectable history, which is itself useful material for `grade.md`'s process notes.

**Path-scoped commits (within a campaign).** One repo per campaign eliminates *cross-campaign* interleaving structurally — but a single campaign's repo can still hold several drills' worth of history in one working tree, so it's still possible to have uncommitted changes sitting in an *inactive* drill's folder within the *same* campaign (e.g. a stray fix to `003-.../` made while working on `004-...`) at the moment the skill is about to commit. Treat this as the edge case it now is, not a structural risk — but the same insurance still applies. Every commit the skill itself makes (the Setup starter commit, the Grade graded commit) **must be staged with an explicit path to the active drill's folder** (`git add NNN-area-slug/`) — never `git add -A` or `git commit -a`. Before staging, check `git status` for changes outside that path:
- If there are none, proceed as normal.
- If there are some, **stop and ask the human** what to do with them — name the files and which drill they appear to belong to. Do not guess, auto-commit, auto-stash, or silently fold them into the active drill's commit (that would make the tagged commit misrepresent what the drill's own attempt actually changed, and would make a future `git diff <tag> <tag>` for the *other* drill noisy or wrong). The human might commit them separately first, revert them, or explicitly say to leave them and proceed anyway — any of those is fine, but it's their call, not an assumption the skill makes.

This keeps every tagged commit strictly scoped to one drill's path, so `git diff <tag> <tag>` stays clean by construction even if unrelated commits (from the human's own doing, outside the skill's commits) end up interleaved in the log between two tags. If reconstructing "what commits happened during this drill" rather than diffing trees, path-filter the log too — `git log NNN-start..HEAD -- NNN-area-slug` — so an interleaved commit from another drill *in the same campaign* doesn't show up as if it were part of this one.
