# Grading Rubric

Score each dimension 1–5 with a one-line justification that **cites specific files/lines**. Then an overall read and the next target. Grade honestly; vague praise wastes the drill.

Scale: **1** can't do it unaided · **2** does it with heavy hints/errors · **3** works but rough/unidiomatic · **4** solid and idiomatic · **5** clean, would impress in a pairing.

## Dimensions

Dimensions 2 and 3 are generic slots — their concrete, stack-appropriate meaning is defined per-campaign in that campaign's `competency-map.md` (see `references/competency-map-guide.md`), generated from the target job description at campaign create time. Read that campaign's map before grading; do not assume these dimensions mean "TypeScript"/"React" by default — a Go campaign's dimension 2 is about idiomatic error handling and interface satisfaction, a Rust campaign's is about ownership/borrowing, and so on.

1. **Correctness** — meets the acceptance criteria; handles the stated edge cases; actually runs.
2. **Language & type-system usage** — types/error-handling model the domain correctly for this stack (see the campaign's competency map for what "correctly" means here — e.g. unions/narrowing and minimal `any` for TypeScript; idiomatic error returns for Go; correct ownership/borrowing for Rust).
3. **Framework & domain idioms** — correct use of this stack's characteristic patterns (see the campaign's competency map — e.g. hook deps/cleanup for React; middleware composition and error-boundary conventions for a Node API; `@State`/`@Binding`/`@Observable` ownership for SwiftUI).
4. **Component / code decomposition** — cohesive units, clean prop/interface boundaries, no god-components, reasonable file structure.
5. **State & data flow** — right state shape, single source of truth, derived-not-duplicated, predictable updates.
6. **Edge cases & robustness** — loading/error/empty, invalid input, async races, failure handling appropriate to the task.
7. **Clarity & idiom currency** — naming, readability, comments where warranted; a stranger could maintain it. Explicitly flag naming/style choices that would read as dated or non-idiomatic to someone with ~5 years of continuous, unassisted experience in this stack (e.g. bare `valid` vs. `isValid`/`isSaving` boolean-naming consistency, `&&`/`||`-chains used where a ternary is safer and more idiomatic, missed destructuring or object-shorthand, etc.). Cite the specific line and name the more idiomatic alternative — this check runs on every grade, not just when asked; the goal is to resurface muscle memory for someone brushing off the dust after a stretch of AI-assisted coding, not to gate on it.
8. **Solo-build convincingness (meta)** — taking the whole submission, would an interviewer believe this person can own and build a system like this alone? This is the headline dimension; it weighs the others.

## Modifiers (note explicitly, don't fold silently)
- **Hints used** — total, highest level, topics. Heavy reliance caps the effective score regardless of the final artifact.
- **Time vs. box** — under / at / over. Slow-but-correct is a different signal than fast-but-rough; name which.
- **Unaided recovery** — did they find and fix their own bugs? Self-debugging is a strong positive.

## Output shape for grade.md
- Per-dimension: score + justification (with line cites).
- **Strengths** (2–3, specific).
- **Weaknesses** (2–3, specific, each with the *why*).
- **Study list** — highest-leverage things to learn next, seeded from hint topics and the weak dimensions.
- **Hints & time summary.**
- **Next drill target** — the area/dimension to attack next, and whether to raise difficulty.
