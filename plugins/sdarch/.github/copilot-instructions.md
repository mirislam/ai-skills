# SDArch — Copilot Instructions

This repository/plugin provides the **SDArch** multi-agent software design workflow.

When the user asks to plan, design, or architect a project or feature using SDArch:
1. Follow `skills/sdarch-workflow/SKILL.md` as the orchestration workflow.
2. Run each agent (`planning-agent`, `architecture-agent`, `review-agent` in `.github/agents/` or `agents/`) in an **isolated context** with no shared conversation memory.
3. Track progress exclusively via `docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, and `docs/review-feedback.md`.
