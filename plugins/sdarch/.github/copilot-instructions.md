# SDArch — Copilot Instructions

This repository/plugin provides the **SDArch** multi-agent software design and architecture review workflow.

When the user asks to plan, design, architect a project/feature, or analyze and review an existing codebase's architecture using SDArch:
1. Follow `skills/sdarch-workflow/SKILL.md` as the orchestration workflow (auto-detecting **Mode A: Software Design** vs. **Mode B: Codebase Architecture Review**).
2. Run each agent (`planning-agent`, `architecture-agent`, `review-agent` in `.github/agents/` or `agents/`) in an **isolated context** with no shared conversation memory.
3. Track progress exclusively via `docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, `docs/architecture-review.md`, and `docs/review-feedback.md`.
4. In Architecture Review Mode, keep application source code strictly read-only—only write `docs/architecture-review.md` and `docs/review-feedback.md`.
