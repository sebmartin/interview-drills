# Campaign create — bootstrap a new campaign

Triggered by an explicit request naming a campaign (e.g. "create a campaign called acme") — never auto-detected or run implicitly by Setup/Solo/Grade. A campaign is a bounded, independent arc of practice (typically one per target job), living at `campaigns/<name>/` as **its own git repo**, independent of the skill's own and of every other campaign's (see `SKILL.md`'s "The repos" section). `campaigns/` itself is just a plain directory — a container for these sibling repos, not a repo itself. One human might run several campaigns at once, one per target application; each gets its own fresh repo, sharing nothing but the parent directory they happen to sit in.

## 0. Refuse if the name already exists

Check for `campaigns/<name>/`. If it exists, **stop and explain** — that campaign already exists. Point at resuming that campaign instead. Never silently overwrite a campaign's `LEDGER.md` or drill history. (If the human's actual intent is "my target job changed, update the context," that's a distinct, smaller operation — confirm that's what they want, then only touch that campaign's `AGENTS.md` Context section, never regenerate its `LEDGER.md`.)

## 1. Repo + campaign directory setup

- Create `campaigns/<name>/` and `git init` a **fresh, independent repo** inside it — every campaign gets its own repo, never shared with another campaign or with the skill. Confirm the parent location with the human first if it's ambiguous where `campaigns/` itself should live.
- If `campaigns/README.md` doesn't exist yet (a plain file, tracked by the *skill* repo, not by any campaign's repo — since `campaigns/` itself isn't a repo), create it: self-contained orientation for any agent that lands in this directory without the skill's own context — what this data is, that it's personal, the campaign shape (each subdirectory its own independent repo), and that summarizing/reading is fine but driving a new drill isn't without the skill's actual protocol. Write it once; never overwrite an existing one.
- Seed `campaigns/<name>/LEDGER.md` from `references/ledger-template.md`, with an **empty** Drills table and an empty Active weaknesses table (rows come from the intake quiz below, not fabricated).

## 2. Intake quiz

Ask the human directly (not via a form/schema — this is a short conversational exchange, not an interrogation). Three things, in this order:

1. **Target context (optional).** "Is there a specific job description or role you're studying toward? Paste it if so — otherwise I'll keep this campaign's drills generic." If given, this seeds domain flavor for premises (company/product language, relevant stack emphasis) AND is the primary input to the competency map built in step 3 below. Not required — plenty of practice arcs are general fluency, not one target; if no job description is given, ask directly which stack(s) to target instead.
2. **Stack.** Genuinely open — do not default to or suggest any particular stack. If a job description was given in step 1, the stack is usually inferable from it (confirm your read rather than re-asking from scratch); a role can legitimately span more than one stack (e.g. a React frontend + a Node.js backend for the same job) — capture that as-is, don't force it into one. This skill has no shipped stack content of any kind; every campaign's competency map (step 3) is generated fresh from what's captured here.
3. **Self-reported starting weaknesses.** Open-ended: "Anything you already know you're rusty on, or specific things you want this campaign to target early?" Take this at face value — do not probe or cross-examine, this is a self-report, not a graded assessment.

## 3. Generate campaigns/<name>/competency-map.md

Build the campaign's competency map now, from the job description and stack(s) captured in the intake quiz — full recipe in `references/competency-map-guide.md`. This is generated **fresh, specific to this campaign** — never copied or adapted from another campaign's map, and this skill ships no pre-built content to draw from. Save it to `campaigns/<name>/competency-map.md`.

If no specific job description was given, still generate a map from the stack(s) named in step 2, and say explicitly in the map's header that it's general-practice rather than job-targeted.

## 4. Write campaigns/<name>/AGENTS.md (and a CLAUDE.md import)

`campaigns/<name>/AGENTS.md` is the single real file holding this campaign's context — no separate `CONTEXT.md`. Claude Code (and other `AGENTS.md`-convention tools) auto-loads `AGENTS.md`/`CLAUDE.md` files up the directory tree, so a session opened inside `campaigns/<name>/` picks this up automatically.

```markdown
## interview-drill

This directory is an `interview-drill` campaign — its own independent git repo, separate from any other campaign's and from the skill's own. Before doing anything drill-related, read `LEDGER.md` (this campaign's across-drills history, active weaknesses, next target) and `competency-map.md` (this campaign's areas and stack-specific rubric meaning). Full skill protocol: `.claude/skills/interview-drill/SKILL.md`, in the skill's own repo (wherever that's installed — not necessarily a fixed relative path from here).

### Context

**Initialized:** <date>

**Target:** <job description pasted verbatim, or "General practice — no specific target job.">

**Stack:** <e.g. "React + TypeScript (frontend), Node.js + TypeScript (backend)" or whatever was specified — full detail lives in `competency-map.md`, this is a short summary>

**Self-reported starting point:** <human's own words on what they think they're rusty on, verbatim or lightly cleaned up — don't editorialize or infer beyond what was said>
```

Then create `campaigns/<name>/CLAUDE.md` containing just `@AGENTS.md` (Claude Code's file-import syntax — pulls that `AGENTS.md`'s content in at read time, so there's only one place to update).

Setup reads this Context section (and `competency-map.md`) to flavor premises toward the target and weight area selection toward self-reported gaps.

## 5. Seed LEDGER.md's Active weaknesses from the self-report

For each concrete, specific item in the self-report (not vague statements — "I don't do much CSS" is specific enough, "I'm rusty in general" is not and shouldn't get a row), add a row to `campaigns/<name>/LEDGER.md`'s Active weaknesses table:

| Weakness | Status | Seen in | Moved by | Note |
|---|---|---|---|---|
| <short name> | self-reported | — | — | <one line, human's own framing> |

Use `Status: self-reported` (not `open`) to distinguish "the human says this" from "a drill actually demonstrated this" — the two carry different evidentiary weight, and Setup/Grade should be honest about that distinction rather than treating a self-report as equivalent to an observed pattern. Once a drill actually exercises and confirms (or contradicts) a self-reported item, update its status to `open`/`resolved` like any other weakness and note which drill provided the real evidence.

## 6. Become the active campaign, then hand off

Write `<name>` to `.last-campaign` — this file lives at the **skill repo's** root, not inside any campaign's own repo (see `SKILL.md`'s Output layout). State **"Working on campaign: `<name>`"**. Confirm what was captured (repeat back the target/stack/weaknesses briefly, and the shape of the generated competency map — how many areas, which stack(s)), state that `campaigns/<name>/LEDGER.md`, `competency-map.md`, and `AGENTS.md` (with `CLAUDE.md` importing it) are ready, and suggest the natural next step: run Setup for the first drill, prioritizing self-reported gaps and/or areas not yet covered. Then stop — creating the campaign does not chain automatically into Setup.
