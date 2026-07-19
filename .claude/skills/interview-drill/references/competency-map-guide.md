# Competency Map Guide — how to build one

A **competency map** is generated **once per campaign**, at campaign create time, from that campaign's target job description (and stack, if given). It is not shared, reused, or drawn from a library — every campaign gets a fresh one, written directly into `campaigns/<name>/competency-map.md`. This guide is the recipe for building a good one; it contains no content of its own, and no stack (React, Go, Node, Swift, whatever) is special-cased in this skill anywhere.

**Why generated fresh, not pulled from a shared library:** competency areas that matter for a React frontend role and a Node backend role barely overlap even though both might say "TypeScript" in the job description — the earlier version of this skill baked one company's competency map (a specific company's, React + Node) directly into the skill's own shared files as if it were a universal default, which broke the moment a second campaign (a different stack, a different role shape) needed something else. A generated-per-campaign map has no such reuse assumption to violate, and building a good one from a job description is squarely within what the AI running this skill is good at doing on the fly.

## Inputs

- **The target job description** — as close to verbatim as the human provided it during the intake quiz. If no specific job was given (general-practice campaign), ask directly what stack(s)/role shape to target, or default to a broad full-stack web competency map and say so explicitly in the map's header.
- **The stack question's answer** from the intake quiz — may name one stack, several (e.g. "React frontend + Node backend," matching how a real full-stack role reads), or be open/unsure (in which case, infer likely stack(s) from the job description's own language before asking a follow-up).

## Structure

A competency map is a markdown file with this shape:

```markdown
# Competency Map — <campaign name>

Generated <date> from the target job description. Stack(s): <e.g. "React + TypeScript (frontend), Node.js + TypeScript (backend)">.

## Areas

| # | Area | Stack | Proves | Difficulty |
|---|---|---|---|---|
| 1 | <area name> | <which stack this area belongs to, if the campaign spans more than one> | <what it demonstrates to an interviewer for THIS job> | easy–med / med / med–hard / hard |
...

## Rubric dimension specifics

**Language & type-system usage** (rubric dimension 2): <what "good" looks like for this stack — e.g. for TypeScript: unions/narrowing, few/no `any`; for Go: idiomatic error handling, interface satisfaction; for Rust: ownership/borrowing used correctly, minimal unnecessary `.clone()`.>

**Framework & domain idioms** (rubric dimension 3): <what "good" looks like for this framework/domain — e.g. for React: hook deps/cleanup, no stale closures; for a Node API: middleware composition, error-boundary conventions; for SwiftUI: state ownership (`@State`/`@Binding`/`@Observable`), view decomposition.>

## Notes on targeting
<1-4 bullets: which areas combine well for "solo-build convincingness" drills, which areas are especially worth over-practicing given this specific job's emphasis, any [legacy/field]-style areas worth rotating in if the role involves inheriting/maintaining existing systems, difficulty-progression guidance (single-area/generous-box early, combined-area/tighter-box later).>
```

## How to build the Areas table

1. **Read the job description for what the day-to-day actually is**, not just the listed tech. A role that says "React, Node.js" but describes "own an internal product end-to-end, from the field-facing UI to the IoT ingestion backend" implies both a frontend competency set (component decomposition, forms, data fetching, lists) AND a backend competency set (routing, validation, error handling, typed boundaries) — treat these as genuinely separate areas, not one blended "full-stack" row, since a drill exercises one or the other, not both loosely at once.
2. **For each stack the campaign covers, generate 6-12 areas** spanning: core language/framework fundamentals, state/data management, the domain's characteristic hard problems (e.g. for a frontend role: async/races/cancellation; for a backend role: concurrency, error propagation, idempotency; for a systems role: memory/ownership, concurrency primitives), and at least one or two **[legacy/field]**-tagged areas (refactoring unfamiliar code, debugging a live system) if the job description signals ownership of an existing/inherited codebase rather than greenfield work — this is a strong, common signal for maintenance-heavy or small-team roles.
3. **Weight toward what the job description actually emphasizes.** A role that repeatedly mentions "own it end to end" or "small team, wear many hats" should weight toward decomposition/architecture-under-ambiguity areas and the [legacy/field] areas; a role emphasizing a specific hard technical problem (real-time data, offline-first, high-scale lists) should have a dedicated area for exactly that problem, not just a generic catch-all.
4. **Each area's "Proves" column should be interviewer-facing language** — what this area demonstrates if executed well, not implementation detail. ("You split UI into cohesive components with clean, well-typed prop boundaries" — not "you know how to use `useState`.")
5. **Difficulty should span the full range** (easy–med through hard) so the campaign can progress — don't generate a flat set of all-medium areas.

## How to fill in the Rubric dimension specifics section

Rubric dimensions 2 and 3 are generic slots in `references/rubric.md` ("Language & type-system usage" and "Framework & domain idioms") — this campaign's competency map is what gives them concrete, stack-appropriate meaning. Write 1-3 sentences per dimension per stack the campaign covers (if the campaign spans multiple stacks, e.g. React + Node, write one paragraph per stack under each dimension, or note where they overlap). Ground this in what an interviewer who's spent ~5 years in that specific stack would actually notice — the same "idiom currency" standard the grading rubric's dimension 7 already applies generically.

## When to regenerate or update

- **Never silently regenerate** an existing competency map — if the human's target job changes mid-campaign, confirm that's the intent (same rule as updating a campaign's `AGENTS.md` Context section), then regenerate deliberately, and note in the campaign's `LEDGER.md` that scoring history before that point was measured against a different map (areas/rubric-specifics may not carry over one-to-one).
- **A stack can be added to an existing campaign's map** (e.g. a specific company's map growing from React-only to React + Node) without a full regeneration — append new areas and a new stack's rubric-dimension-specifics paragraph, don't rewrite what's already there.
