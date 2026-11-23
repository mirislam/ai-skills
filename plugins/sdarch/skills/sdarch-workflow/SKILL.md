---
name: sdarch-workflow
description: >-
  Orchestrates the three-agent, memory-isolated software design and architecture
  review workflow (Planning Agent, Architecture Agent, Review Agent). Supports
  two modes: (1) Design Mode to transform a user idea into reviewed
  docs/inspiration.md, docs/requirements.md, and a coding-agent-ready
  docs/architecture.md; and (2) Architecture Review Mode to analyze an existing
  codebase (and any arch*.md files), perform a qualitative architectural
  assessment, and write coding-agent-ready improvement recommendations to
  docs/architecture-review.md. Use when the user asks to plan, design,
  architect, analyze existing code, review an architecture, or run the sdarch
  workflow.
---

# SDArch Workflow Orchestrator

This skill coordinates three independent, stateless agents (`planning-agent`, `architecture-agent`, `review-agent`) that communicate **only** through markdown documents on disk (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, `docs/architecture-review.md`, and `docs/review-feedback.md`).

## Core Invariant: Zero Shared Memory

- Each agent run **MUST** be launched in a fresh, isolated subagent/session (e.g., `invoke_subagent` in Antigravity, `Agent` tool in Claude Code, or an isolated subagent/task in Codex or Copilot).
- **Never** pass prior agents' chain-of-thought or conversation transcripts in the subagent prompt. Pass **only** the relevant file paths, scope, and (where applicable) the user's original request.
- Assess current progress by checking which files already exist in `docs/`.

---

## Step 0: Detect Workflow Mode

Inspect the user's request to select the workflow mode:

- **Mode A — Software Design Pipeline (`docs/inspiration.md` → `docs/requirements.md` → `docs/architecture.md`):**
  Trigger when the user asks to plan, design, spec out, or architect a new project, app, or feature.
- **Mode B — Codebase Architecture Review (`docs/architecture-review.md`):**
  Trigger when the user asks to analyze existing code, review or audit the current architecture, check alignment against an architecture doc (`arch*.md`), or recommend architectural improvements.
- If the user's intent is ambiguous (e.g., an existing repo with a vague request), ask a brief clarifying question before starting.

---

## Mode A: Software Design Pipeline

### Determine Starting Phase

Check the workspace `docs/` directory:
- If `docs/inspiration.md` does not exist → start at **Phase 1**.
- If `docs/inspiration.md` exists and `docs/requirements.md` does not → start at **Phase 2** (unless `docs/review-feedback.md` indicates `docs/inspiration.md` needs revision).
- If `docs/requirements.md` exists and `docs/architecture.md` does not → start at **Phase 3** (unless `docs/review-feedback.md` indicates `docs/requirements.md` needs revision).
- If the user provides a brand-new goal and asks to start fresh, confirm before overwriting existing `docs/*.md` files.

### Phase 1: Inspiration (`docs/inspiration.md`)

1. **Invoke `planning-agent`** in a fresh context:
   - Provide: the user's goal statement + path to `skills/sdarch-workflow/references/inspiration-template.md`.
   - Instruct it to write `docs/inspiration.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: the user's original goal statement + path to `docs/inspiration.md` + path to `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md` with verdict `APPROVED` or `REVISION_REQUIRED`.
3. **Gate:** If `REVISION_REQUIRED`, re-invoke `planning-agent` (fresh context) with `docs/inspiration.md` and `docs/review-feedback.md`, then re-run `review-agent`. Repeat up to **3 iterations**; if still not approved, pause and escalate to the user.

### Phase 2: Requirements (`docs/requirements.md`)

1. **Invoke `planning-agent`** in a fresh context:
   - Provide: **only** the path to `docs/inspiration.md` + `skills/sdarch-workflow/references/requirements-template.md`.
   - Instruct it to write `docs/requirements.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, and `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md`.
3. **Gate:** If `REVISION_REQUIRED`, loop `planning-agent` → `review-agent` up to **3 iterations**, escalating to the user if unresolved.

### Phase 3: Architecture (`docs/architecture.md`)

1. **Invoke `architecture-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, and `skills/sdarch-workflow/references/architecture-template.md`.
   - Instruct it to inspect any existing codebase if applicable and write `docs/architecture.md`.
2. **Invoke `review-agent`** in a fresh context:
   - Provide: paths to `docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, and `skills/sdarch-workflow/references/review-rubric.md`.
   - Instruct it to write `docs/review-feedback.md`.
3. **Gate:** If `REVISION_REQUIRED`, loop `architecture-agent` → `review-agent` up to **3 iterations**, escalating to the user if unresolved.

### Mode A Final Handoff

Once `docs/architecture.md` is `APPROVED`:
1. Summarize the three generated documents (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`) and any key assumptions made.
2. Inform the user that `docs/architecture.md` is ready to be passed directly to any coding agent (Codex, Claude Code, Gemini, or Copilot) for implementation.

---

## Mode B: Codebase Architecture Review (`docs/architecture-review.md`)

This 2-stage isolated pipeline evaluates an existing codebase, audits any architecture documentation, and writes actionable recommendations for a human engineer or coding agent.

**Strict Read-Only Rule on Code:** Neither agent may modify application source code during this workflow. Only `docs/architecture-review.md` and `docs/review-feedback.md` are created or updated.

### Step B0: Discover Architecture Docs & Scope

1. Determine the review scope from the user's prompt (default: full repository; or a specific directory/subsystem if the user requested one).
2. Search the repository (especially repo root and `docs/`) case-insensitively for existing architecture documents matching `arch*.md` (e.g., `ARCHITECTURE.md`, `arch.md`, `docs/architecture.md`, `docs/arch-*.md`). **Exclude `docs/architecture-review.md`** so prior review outputs are not treated as architecture specs.
3. Also note if `docs/requirements.md`, `docs/inspiration.md`, or `README.md` exist as helpful context.

### Stage 1: Codebase Analysis & Recommendations (`architecture-agent`)

1. **Invoke `architecture-agent` (Architecture Review Mode)** in a fresh context:
   - Provide:
     - The user's review request and target scope (full repo or specific path)
     - Paths to all discovered `arch*.md` files (or explicitly state `"None found"`)
     - Paths to any supporting context docs (`docs/requirements.md`, `docs/inspiration.md`) if present
     - Path to `skills/sdarch-workflow/references/architecture-review-template.md`
   - Instruct it to:
     - Inspect the codebase (read-only) to map the real as-is architecture
     - Read and analyze every discovered `arch*.md` file for design quality and spec-vs-code drift
     - Perform a qualitative architectural assessment across modularity, coupling/cohesion, data/state, interfaces, scalability/reliability, security/error handling, and testability
     - Produce prioritized, evidence-backed recommendations (`REC-*`) and an ordered, coding-agent-ready Implementation Roadmap in `docs/architecture-review.md`

### Stage 2: Independent Verification Gate (`review-agent`)

1. **Invoke `review-agent` (Architecture Review Audit)** in a fresh context:
   - Provide:
     - The user's review request and target scope
     - Path to `docs/architecture-review.md`
     - Paths to all discovered `arch*.md` files (if any)
     - Path to `skills/sdarch-workflow/references/review-rubric.md`
   - Instruct it to:
     - Independently verify findings, file paths, and drift claims directly against the actual codebase and `arch*.md` (catching any hallucinated paths, shallow critiques, or missed architecture docs)
     - Audit `docs/architecture-review.md` against the **Architecture Review Rubric** in `skills/sdarch-workflow/references/review-rubric.md`
     - Write `docs/review-feedback.md` with verdict `APPROVED` or `REVISION_REQUIRED`
2. **Gate:** If `REVISION_REQUIRED`, re-invoke `architecture-agent` (fresh context, Architecture Review Mode) with `docs/architecture-review.md`, `docs/review-feedback.md`, and the discovered `arch*.md` paths, then re-run `review-agent`. Repeat up to **3 iterations**; if still not approved, pause and escalate to the user.

### Mode B Final Handoff

Once `docs/architecture-review.md` is `APPROVED`:
1. Present a concise summary of:
   - Overall architectural health verdict
   - Key strengths and structural risks discovered
   - Architecture doc (`arch*.md`) alignment/drift highlights (if applicable)
   - Top prioritized recommendations (`REC-*`)
2. Inform the user that `docs/architecture-review.md` has been saved and can be reviewed directly or passed to any coding agent (Codex, Claude Code, Gemini, or Copilot) to execute the refactoring milestones.
