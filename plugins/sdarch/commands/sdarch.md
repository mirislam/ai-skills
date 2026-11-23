---
description: Run the SDArch 3-agent, memory-isolated workflow to either design a new project/feature (inspiration → requirements → architecture) or review an existing codebase's architecture and write coding-agent-ready recommendations to docs/architecture-review.md.
argument-hint: "<project/feature idea OR 'review [optional path/focus]' to analyze existing code>"
---

Run the `sdarch-workflow` skill (`skills/sdarch-workflow/SKILL.md`) for the following user request:

$ARGUMENTS

Auto-detect the appropriate mode from the request:
- **Mode A (Software Design Pipeline):** For new project/feature planning, requirements, and architecture (`docs/inspiration.md` → `docs/requirements.md` → `docs/architecture.md`).
- **Mode B (Codebase Architecture Review):** For analyzing an existing codebase (plus any `arch*.md` docs) and producing a qualitative assessment and improvement recommendations in `docs/architecture-review.md` (read-only on application code).

Strictly enforce zero shared memory between subagents: each invocation of `planning-agent`, `architecture-agent`, and `review-agent` must run in a fresh context with only the relevant file paths and scope passed in.
