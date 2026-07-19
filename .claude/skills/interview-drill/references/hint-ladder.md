# Hint Ladder — the no-crutch protocol

The point of Solo mode is to find out what the human can do *without* the AI at the keyboard. Hints exist so a stuck human isn't wasted time, but every hint is a signal about a gap, so every hint is logged and costs rubric points. Climb the ladder one rung at a time; never skip to code.

## The ladder

- **L1 — Nudge.** Point at *where* to look, not what to do. "Something about how you're updating that state looks off — re-read how you set it." Cost: negligible.
- **L2 — Concept.** Explain the underlying idea in general terms, on no specific code. "When state depends on the previous value, prefer the updater form so you don't read a stale value." Cost: minor.
- **L3 — Name the API / pattern.** Name the tool but not the usage in their code. "This is what `useReducer` (or `AbortController`, or a discriminated union) is for." Cost: moderate.
- **L4 — Worked analogy on DIFFERENT code.** Show the pattern on an unrelated toy example the human must then translate to their problem themselves. Cost: significant.
- **L5 — The actual solution code.** ❌ Never. Not in Solo mode. If the human truly cannot proceed after L4, stop the drill and mark it "blocked at <topic>" — that blocked topic is the single most valuable output of the session and becomes the next study target.

## Allowed vs. forbidden in Solo mode

**Allowed:**
- Explaining a concept, syntax rule, or API in the abstract.
- Comparing two approaches the human proposes (in words).
- Clarifying the task / acceptance criteria.
- Pointing to official docs.
- Sanity-checking a plan the human describes verbally.

**Forbidden:**
- Writing, pasting, or completing any solution code.
- Giving a line-level implementation, even "as an example," for their actual task.
- Reading their file and telling them the specific fix / the exact line to change.
- Naming the precise bug in their code (downgrade to L1: point at the region, let them find it).
- Pseudo-code detailed enough to transcribe.

## Logging

For each hint, record `Lx — <topic>` (e.g., `L3 — AbortController for fetch cancellation`). At grade time these roll up into: total hints, highest level reached, and topics — which directly seed the study list and the next-drill target. A drill finished with zero hints at a tight time box is the real target state.

## For the AI: handling frustration

The human may push for the answer when stuck. Hold the line kindly. Offer the next rung, or suggest a 5-minute break, or propose narrowing scope — never the code. Solving it for them feels helpful and is the one thing that guarantees the drill taught nothing.
