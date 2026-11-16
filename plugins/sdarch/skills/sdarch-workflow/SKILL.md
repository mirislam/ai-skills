---
name: sdarch-workflow
description: >-
  Orchestrates the three-agent, memory-isolated software design workflow
  (Planning Agent, Architecture Agent, Review Agent) to transform a user idea
  into reviewed docs/inspiration.md, docs/requirements.md, and a coding-agent-ready
  docs/architecture.md. Use when the user asks to plan, design, architect, or
  run the sdarch workflow for a new feature or application.
---

# SDArch Workflow Orchestrator

This skill coordinates three independent, stateless agents that communicate **only** through markdown documents on disk (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, and `docs/review-feedback.md`).

## Core Invariant: Zero Shared Memory

- Each agent run **MUST** be launched in a fresh, isolated subagent/session (e.g., `invoke_subagent` in Antigravity, `Agent` tool in Claude Code, or an isolated subagent/task in Codex or Copilot).
- **Never** pass prior agents' chain-of-thought or conversation transcripts in the subagent prompt. Pass **only** the file paths of the relevant documents and (for Phase 1) the user's original goal statement.
- Assess current progress by checking which files already exist in `docs/`.

---

## Pipeline Execution

### Step 0: Determine Starting Phase

Check the workspace `docs/` directory:
- If `docs/inspiration.md` does not exist → start at **Phase 1**.
- If `docs/inspiration.md` exists and `docs/requirements.md` does not → start at **Phase 2** (unless `docs/review-feedback.md` indicates `docs/inspiration.md` needs revision).
- If `docs/requirements.md` exists and `docs/architecture.md` does not → start at **Phase 3** (unless `docs/review-feedback.md` indicates `docs/requirements.md` needs revision).
- If the user provides a brand-new goal and asks to start fresh, confirm before overwriting existing `docs/*.md` files.

---

### Phase 1: Inspiration (`docs/inspiration.md`)

1. **Invoke `planning-agent`** in a fresh context:
   - Provide: the user's goal statement + path to `skills/sdarch-workflow/references/inspiration-template.md`.
   - Instruct it to write `docs/inspiration.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: the user's original goal statement + path to `docs/inspiration.md` + path to `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md` with verdict `APPROVED` or `REVISION_REQUIRED`.
3. **Gate:** If `REVISION_REQUIRED`, re-invoke `planning-agent` (fresh context) with `docs/inspiration.md` and `docs/review-feedback.md`, then re-run `review-agent`. Repeat up to **3 iterations**; if still not approved, pause and escalate to the user.

---

### Phase 2: Requirements (`docs/requirements.md`)

1. **Invoke `planning-agent`** in a fresh context:
   - Provide: **only** the path to `docs/inspiration.md` + `skills/sdarch-workflow/references/requirements-template.md`.
   - Instruct it to write `docs/requirements.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, and `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md`.
3. **Gate:** If `REVISION_REQUIRED`, loop `planning-agent` → `review-agent` up to **3 iterations**, escalating to the user if unresolved.

---

### Phase 3: Architecture (`docs/architecture.md`)

1. **Invoke `architecture-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, and `skills/sdarch-workflow/references/architecture-template.md`.
   - Instruct it to inspect any existing codebase if applicable and write `docs/architecture.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, and `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md`.
3. **Gate:** If `REVISION_REQUIRED`, loop `architecture-agent` → `review-agent` up to **3 iterations**, escalating to the user if unresolved.

---

## Final Handoff

Once `docs/architecture.md` is `APPROVED`:
1. Summarize the three generated documents (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`) and any key assumptions made.
2. Inform the user that `docs/architecture.md` is ready to be passed directly to any coding agent (Codex, Claude Code, Gemini, or Copilot) for implementation.
