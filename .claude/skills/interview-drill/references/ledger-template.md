# Ledger Template

Copy this to `LEDGER.md` at the root of a campaign's own repo (`campaigns/<name>/`, its own independent git repo — see `SKILL.md`'s "The repos" section) when that campaign is created. Each campaign has its own independent ledger — read it in Setup (pick the weakest area / rising difficulty); update it in Grade. Never merge or compare ledgers across campaigns; a weakness tracked in one says nothing about another.

**Design principle: this file is a scannable index, not a narrative.** Full context for any drill lives in that drill's `grade.md` and git history — link/reference it, don't re-narrate it here. Weaknesses are tracked as status rows with drill tags, not growing paragraphs.

```markdown
# Interview Drill — Ledger

Across-drills tracker. Scores are 1–5 per rubric dimension (Corr / Lang / Frame / Decomp / State / Edge / Clarity / Solo — dimensions 2 "Lang" and 3 "Frame" are generic slots whose concrete meaning for this campaign's stack(s) is defined in `competency-map.md`, see `references/rubric.md`). "Hints" = total (highest level). Read before a new drill to target gaps; update after grading. Full context for any drill lives in its `grade.md` and git history (`git log`, `git show <tag>:<path>`) — this file stays a scannable index, not a narrative.

## Drills

| ID | Date | Area(s) | Time (used/box) | Hints | Corr | Lang | Frame | Decomp | State | Edge | Clarity | Solo | Next target |
|----|------|---------|-----------------|-------|------|------|-------|--------|-------|------|---------|------|-------------|
| 001 | | | | | | | | | | | | | |

## Active weaknesses

One row per tracked issue. `Seen in` = drills where it showed up; `Moved by` = drills that made it better(↑)/worse(↓). Details live in that drill's `grade.md` / commit message — look there, not here, for the full story.

| Weakness | Status | Seen in | Moved by | Note |
|---|---|---|---|---|
| <short name> | open / open, watch / regressed | <drill ids> | <drill id>(↑ or ↓ + why in ~3 words) | <one line, current state only> |

## Improvements (resolved weaknesses — tracked for regression)

Concise by design — one short paragraph per item. If something here recurs, move it back to *Active weaknesses* rather than editing history here.

### <weakness name>
**Resolved since:** <drill id> (regression watch: <none so far / what to watch>)
<2-4 sentences: what it was, which drill(s) fixed it, current confidence>

## Areas not yet covered
<from this campaign's competency-map.md, comma-separated>

## Difficulty trend
<are we tightening the box / combining areas yet?>

## Blocked topics
<drills that hit L5 / couldn't proceed — the highest-priority study items, or "(none)">

## Standing strengths
<durable, cross-drill strengths — keep concise, this is not where new strengths get their first mention (that's grade.md); only promote here once seen 2+ times>

## Tooling notes
<anything about the drill mechanics/scaffold/tooling itself worth remembering, not about Seb's code>
```

## How the AI should use it

- **Setup:** scan *Active weaknesses* and *Areas not yet covered* to pick the target; prefer open weaknesses with no recent movement, and uncovered areas, over re-testing something already resolved. Raise difficulty when a dimension has been ≥4 twice.
- **Grade:**
  1. Append **one** new row to **Drills** per drill — even if the human reworked the code across several rounds of discussion before confirming the grade (see `SKILL.md`'s Mode 3), that's still one drill, one row. If it took more than one round, note the revision count briefly in the Area(s) cell (e.g. `data fetching + lists (#4, #5), 2 revisions before final`) rather than adding `NNN`/`NNNb`-style rows or duplicate ids for the same drill.
  2. For each weakness surfaced this drill: if it's new, add a row to **Active weaknesses**. If it already has a row, update `Seen in`/`Moved by`/`Note` in place — don't add a duplicate row or a second paragraph of history; the note reflects *current* state only.
  3. If a weakness is now resolved (no recurrence across 2+ drills where it had a real chance to show up, or a structural fix like a lint rule that makes recurrence unlikely), **move its row** from *Active weaknesses* to a new subsection under **Improvements**, written as the concise fixed-length paragraph shown above. Don't leave it in both places.
  4. If a resolved item recurs, move it back to *Active weaknesses* with `Status: regressed` and reference which drill broke it — do not silently re-resolve without noting the regression happened.
  5. Refresh **Areas not yet covered**, **Difficulty trend**, **Blocked topics** as needed.
  6. Only add to **Standing strengths** once a strength has shown up across 2+ drills — a single drill's strengths belong in that drill's `grade.md`, not here.
