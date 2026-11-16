# SDArch — Document-Driven Multi-Agent Development Workflow

This workspace/plugin provides a three-agent, memory-isolated software design pipeline:

1. **Planning Agent** (`agents/planning-agent.md`)
   - Phase 1: Expands a raw user goal into `docs/inspiration.md`.
   - Phase 2: Reads `docs/inspiration.md` (in a fresh context) and produces `docs/requirements.md`.
2. **Architecture Agent** (`agents/architecture-agent.md`)
   - Phase 3: Reads `docs/inspiration.md` and `docs/requirements.md` (in a fresh context) and produces a coding-agent-ready `docs/architecture.md`.
3. **Review Agent** (`agents/review-agent.md`)
   - Gatekeeper after every phase: verifies each document against the phase rubric and upstream goals. If gaps exist, writes actionable feedback to `docs/review-feedback.md` and requests revisions from the responsible agent.

## Strict Isolation Rule

- Every agent invocation **MUST** run in a fresh, independent context with **no shared conversation memory** from previous agent runs.
- Progress and state are determined **solely** from the documents on disk (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, `docs/review-feedback.md`).
- Follow [skills/sdarch-workflow/SKILL.md](./skills/sdarch-workflow/SKILL.md) to orchestrate the pipeline.
