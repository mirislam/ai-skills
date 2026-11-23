# Review Rubric (`docs/review-feedback.md`)

Use these criteria when auditing each phase. Write findings to `docs/review-feedback.md`.

## Phase 1 Rubric: `docs/inspiration.md`
- [ ] Faithfully reflects the user's original goal without drifting off-scope.
- [ ] Fills in underspecified gaps with concrete personas, user journeys, and edge cases.
- [ ] Explicitly separates inferred assumptions from stated requirements and defines non-goals.
- [ ] Clear enough that an independent agent reading *only* `docs/inspiration.md` can write requirements.

## Phase 2 Rubric: `docs/requirements.md`
- [ ] Every core capability and journey in `docs/inspiration.md` is represented by numbered `FR-*` items.
- [ ] Every `FR-*` and `NFR-*` has objective, testable acceptance criteria.
- [ ] No internal contradictions, vague "etc." placeholders, or unaddressed non-goals.
- [ ] Clear enough that an independent architect reading `docs/inspiration.md` + `docs/requirements.md` can design the system.

## Phase 3 Rubric: `docs/architecture.md`
- [ ] Every `FR-*` and `NFR-*` in `docs/requirements.md` appears in the Requirement Traceability Matrix.
- [ ] Concrete tech stack, directory layout, data models, and API/CLI/UI contracts are specified (no "TBD").
- [ ] Ordered implementation milestones and verification commands are actionable for a coding agent (Codex, Claude Code, Gemini, Copilot).
- [ ] Security, error handling, and edge cases are explicitly designed.

## Architecture Review Rubric: `docs/architecture-review.md`
- [ ] Every architectural claim, module description, file path, and finding is verified against the actual codebase (no hallucinated paths, symbols, or behaviors).
- [ ] If any `arch*.md` file exists in the repository (e.g., `ARCHITECTURE.md`, `arch.md`, `docs/architecture.md`, excluding `docs/architecture-review.md`), it was read and analyzed for both design soundness and spec-vs-code drift.
- [ ] The qualitative assessment thoughtfully evaluates architectural fitness, strengths, weaknesses, and trade-offs across key quality dimensions—not just surface-level style nitpicks.
- [ ] Every recommendation (`REC-*`) is prioritized, grounded in evidence, non-contradictory, and states clear trade-offs and verifiable acceptance criteria.
- [ ] The Implementation Roadmap (ordered milestones, exact target files, concrete steps, and verification commands) is safe and unambiguous enough for a human or coding agent to execute incrementally.
- [ ] Application source code was **not** modified during the review (read-only code inspection).

## Required Output Format for `docs/review-feedback.md`

```markdown
# SDArch Review Feedback

- **Target Document:** `<docs/inspiration.md | docs/requirements.md | docs/architecture.md | docs/architecture-review.md>`
- **Responsible Agent:** `<planning-agent | architecture-agent>`
- **Verdict:** `<APPROVED | REVISION_REQUIRED>`

## Blocking Issues (Must Fix)
1. **<Issue Title>:** <Specific gap, unverified claim, or mismatch and exact instruction on how to fix it.>

## Non-Blocking Suggestions (Optional)
- <Optional improvement>
```
