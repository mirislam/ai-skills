# Changelog

All notable changes to **SDArch** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-11-22

### Added
- **Codebase Architecture Review Workflow (Mode B):**
  - Auto-detection between **Mode A (Software Design Pipeline)** and **Mode B (Codebase Architecture Review)** in `skills/sdarch-workflow/SKILL.md` and `/sdarch`.
  - 2-stage, memory-isolated review pipeline: `architecture-agent` inspects the existing codebase (strictly read-only on source files) and writes `docs/architecture-review.md`, followed by independent codebase verification by `review-agent` (up to 3 revision loops).
  - Automatic case-insensitive discovery and analysis of existing `arch*.md` documentation (e.g., `ARCHITECTURE.md`, `arch.md`, `docs/architecture.md`, excluding `docs/architecture-review.md`) to evaluate documented design quality and spec-vs-code drift.
  - Optional scoping to a specific subdirectory or subsystem (defaulting to the full repository).
- **Architecture Review Template:** Added `skills/sdarch-workflow/references/architecture-review-template.md` featuring an executive verdict, as-is architecture map, `arch*.md` drift analysis, 7-dimension qualitative assessment, strengths to preserve, prioritized recommendations (`REC-*`), an ordered coding-agent-ready Implementation Roadmap with verification commands, and a traceability matrix.
- **Architecture Review Rubric:** Added audit criteria for `docs/architecture-review.md` in `skills/sdarch-workflow/references/review-rubric.md`, including factual codebase grounding and read-only compliance checks.

### Changed
- Extended `agents/architecture-agent.md` and `agents/review-agent.md` (and their `.github/agents/*.agent.md` Copilot wrappers) to support Architecture Review Mode alongside Design Mode.
- Updated `commands/sdarch.md`, `AGENTS.md`, `.github/copilot-instructions.md`, and `README.md` with Mode B workflow diagrams, artifact documentation, and usage examples.
- Bumped plugin manifests (`plugin.json` and `.claude-plugin/plugin.json`) to `1.1.0` and added `architecture-review` and `code-analysis` keywords.

## [1.0.0] - 2026-11-15

### Added
- Initial release of **SDArch**, a cross-platform, memory-isolated, document-driven multi-agent software design workflow supporting Antigravity (Gemini), Claude Code, OpenAI Codex, and GitHub Copilot.
- Three independent subagents (`planning-agent`, `architecture-agent`, `review-agent`) communicating exclusively through disk artifacts (`docs/inspiration.md`, `docs/requirements.md`, `docs/architecture.md`, `docs/review-feedback.md`).
- Document templates (`inspiration-template.md`, `requirements-template.md`, `architecture-template.md`), review rubric (`review-rubric.md`), `/sdarch` slash command, and Apache-2.0 license.
