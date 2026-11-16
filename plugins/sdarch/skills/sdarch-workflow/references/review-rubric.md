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

## Required Output Format for `docs/review-feedback.md`

```markdown
# SDArch Review Feedback

- **Target Document:** `<docs/inspiration.md | docs/requirements.md | docs/architecture.md>`
- **Responsible Agent:** `<planning-agent | architecture-agent>`
- **Verdict:** `<APPROVED | REVISION_REQUIRED>`

## Blocking Issues (Must Fix)
1. **<Issue Title>:** <Specific gap or mismatch and exact instruction on how to fix it.>

## Non-Blocking Suggestions (Optional)
- <Optional improvement>
```
