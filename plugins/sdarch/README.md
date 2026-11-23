# SDArch — Document-Driven Multi-Agent Software Design & Architecture Review Plugin

**SDArch** is a cross-platform plugin for daily software development that supports two workflows powered by **three independent, memory-isolated agents**:

1. **Software Design Pipeline (Mode A)** — Turns a brief, rough idea into a thoroughly vetted, coding-agent-ready `docs/architecture.md`.
2. **Codebase Architecture Review (Mode B)** — Analyzes an existing codebase (plus any `arch*.md` docs), performs a qualitative assessment of the chosen architecture, and writes prioritized, coding-agent-ready refactoring recommendations to `docs/architecture-review.md`.

### The Three Independent Agents
1. **Planning Agent** (`planning-agent`) — Expands your raw goal into a rich `docs/inspiration.md`, then (in a separate, fresh run) translates it into testable `docs/requirements.md`.
2. **Design & Architecture Agent** (`architecture-agent`) — In Design Mode, reads `docs/inspiration.md` and `docs/requirements.md` to produce a concrete `docs/architecture.md`. In Architecture Review Mode, inspects the existing codebase and any `arch*.md` files (read-only on source code) to produce `docs/architecture-review.md`.
3. **Review Agent** (`review-agent`) — Acts as an independent quality gate after every phase and architecture review, auditing alignment, factual codebase grounding, and completeness in `docs/review-feedback.md`, sending work back for revision when gaps are found.

---

## Why We Built SDArch

I created SDArch because I was getting tired of constantly prompting agents to ask clarifying questions, challenge their own assumptions, and re-review the work they had already done—all via manual prompts. Doing that by hand did not scale. This small, compact plugin automates that discipline so everything stays aligned without bloating the coding agents' context windows.

When developers jump straight from a 2-sentence prompt into code generation—or ask a single agent to critique an existing repo—two things go wrong:
1. **Context Drift & Sycophancy:** A single long-running agent remembers its own shortcuts, rationalizes missing edge cases, or makes unverified claims about code it skimmed.
2. **Underspecified Handoffs:** Coding agents write brittle code or risky refactors when requirements, module boundaries, and verification steps are left implicit.

**SDArch solves this with strict memory isolation and document-driven state:**
- **Zero Shared Memory:** Every agent invocation starts in a fresh context with **no memory** of previous agent runs. It can only judge progress and make decisions from the markdown documents and code on disk.
- **Built-in Adversarial Review:** Because the Review Agent has no memory of *why* the Planning or Architecture Agent made a choice, it evaluates the documents strictly on what is written—and in Review Mode, independently spot-checks claims against the actual codebase before approving.
- **Coding-Agent Ready Output:** Both `docs/architecture.md` and `docs/architecture-review.md` include exact file paths, concrete design/refactoring recommendations, traceability matrices, and ordered build/refactoring milestones with verification commands.

---

## How the Workflows Work

### Mode A: Software Design Pipeline

```text
User Goal
   │
   ▼
[Phase 1: Planning Agent] ──► docs/inspiration.md ──► [Review Agent] ──(REVISE)──┐
                                                             │                    │
                                                         (APPROVED) ◄─────────────┘
                                                             ▼
[Phase 2: Planning Agent] ──► docs/requirements.md ─► [Review Agent] ──(REVISE)──┐
  (fresh context, reads only inspiration.md)                 │                    │
                                                         (APPROVED) ◄─────────────┘
                                                             ▼
[Phase 3: Architecture Agent] ► docs/architecture.md ► [Review Agent] ─(REVISE)──┐
  (fresh context, reads inspiration + requirements)          │                    │
                                                         (APPROVED) ◄─────────────┘
                                                             ▼
                                       Hand off docs/architecture.md to Codex /
                                         Claude Code / Gemini / Copilot
```

### Mode B: Codebase Architecture Review

```text
User Review Request (full repo or optional path/subsystem)
   │
   ▼
Discover arch*.md (e.g., ARCHITECTURE.md, docs/architecture.md; excludes architecture-review.md)
   │
   ▼
[Stage 1: Architecture Agent] ──► docs/architecture-review.md ──► [Stage 2: Review Agent] ──(REVISE)──┐
  (fresh context, read-only on code,                                (fresh context, verifies claims    │
   analyzes code + arch*.md)                                         against real codebase + arch*.md) │
                                                                           │                           │
                                                                       (APPROVED) ◄────────────────────┘
                                                                           ▼
                                               Review directly or hand off docs/architecture-review.md
                                                  to Codex / Claude Code / Gemini / Copilot
```

### Generated Artifacts (`docs/`)
- `docs/inspiration.md` — Expanded product vision, personas, user journeys, edge cases, inferred assumptions, and non-goals.
- `docs/requirements.md` — Numbered functional (`FR-*`) and non-functional (`NFR-*`) requirements with acceptance criteria.
- `docs/architecture.md` — Concrete system design, file tree, schemas, interfaces, traceability matrix, and implementation milestones.
- `docs/architecture-review.md` — As-is architecture map, `arch*.md` spec-vs-code drift analysis, qualitative assessment across core quality dimensions, prioritized recommendations (`REC-*`), and ordered refactoring milestones for coding agents.
- `docs/review-feedback.md` — Latest Review Agent audit (`APPROVED` or `REVISION_REQUIRED`) with actionable blocking issues.

---

## Repository Structure (Multi-Platform)

SDArch uses a single shared core (`skills/` and `agents/`) with lightweight manifests/wrappers for each agentic platform:

```text
sdarch/
├── plugin.json                        # Antigravity plugin manifest
├── .claude-plugin/plugin.json         # Claude Code plugin manifest
├── AGENTS.md                          # Universal instructions (Codex, Antigravity, etc.)
├── LICENSE-2.0.txt                    # Apache License 2.0
├── commands/sdarch.md                 # /sdarch slash command (Claude Code)
├── agents/                            # Canonical isolated subagent definitions
│   ├── planning-agent.md
│   ├── architecture-agent.md
│   └── review-agent.md
├── skills/sdarch-workflow/            # Open Agent Skills standard
│   ├── SKILL.md                       # Pipeline orchestrator (Design & Review modes)
│   └── references/                    # Document templates & review rubric
│       ├── inspiration-template.md
│       ├── requirements-template.md
│       ├── architecture-template.md
│       ├── architecture-review-template.md
│       └── review-rubric.md
└── .github/                           # GitHub Copilot instructions & agent wrappers
    ├── copilot-instructions.md
    └── agents/
        ├── planning-agent.agent.md
        ├── architecture-agent.agent.md
        └── review-agent.agent.md
```

---

## Installation

### 1. Antigravity
Install globally by linking or copying `sdarch` into `~/.gemini/config/plugins/`:
```bash
ln -s /path/to/sdarch ~/.gemini/config/plugins/sdarch
```
Or per-project inside `.agents/plugins/sdarch`.

### 2. Claude Code
Load directly as a local plugin:
```bash
claude --plugin-dir /path/to/sdarch
```
Or install into your project/user plugins directory so `/sdarch` and the `sdarch-workflow` skill are available.

### 3. OpenAI Codex CLI
Codex discovers skills in `~/.codex/skills/` (or `.agents/skills/` in a project) and reads `AGENTS.md`:
```bash
mkdir -p ~/.codex/skills
ln -s /path/to/sdarch/skills/sdarch-workflow ~/.codex/skills/sdarch-workflow
```
Optionally copy `AGENTS.md` (or reference `sdarch-workflow`) in your project root.

### 4. GitHub Copilot (VS Code / Copilot CLI)
Copy `.github/copilot-instructions.md`, `.github/agents/`, `agents/`, and `skills/sdarch-workflow/` into your repository (or symlink `skills/sdarch-workflow` into `.agents/skills/sdarch-workflow`).

---

## Usage

### Starting a New Feature or App (Design Mode)
Prompt your agent with your idea—even if it is brief:
> "Use `sdarch-workflow` to plan and architect a local-first CLI bookmark manager with full-text search and browser sync."

In Claude Code, you can also use the slash command:
```text
/sdarch A local-first CLI bookmark manager with full-text search and browser sync
```

The orchestrator auto-detects Design Mode and runs Phases 1 → 3 automatically, looping with the Review Agent (up to 3 iterations per phase) until `docs/architecture.md` is approved.

### Reviewing an Existing Codebase's Architecture (Architecture Review Mode)
Prompt your agent to analyze your code and review the architecture:
> "Use `sdarch-workflow` to analyze this codebase and review the architecture."

Or scope it to a specific subsystem:
> "Review the architecture of `src/billing` using `sdarch-workflow`."

In Claude Code, use the same `/sdarch` command:
```text
/sdarch review the architecture of this codebase
/sdarch review src/billing architecture
```

The orchestrator auto-detects Architecture Review Mode, discovers any existing `arch*.md` files (such as `ARCHITECTURE.md` or `docs/architecture.md`), runs the read-only `architecture-agent` analysis followed by `review-agent` verification (up to 3 loops), and writes the approved recommendations to `docs/architecture-review.md`.

### Resuming or Re-running a Phase
Because state lives purely on disk in `docs/`:
- Edit `docs/inspiration.md`, delete `docs/requirements.md` and `docs/architecture.md`, and re-run `sdarch-workflow` to regenerate downstream docs.
- Or jump straight to Phase 3 if you already wrote `docs/inspiration.md` and `docs/requirements.md`.
- Re-running an architecture review overwrites `docs/architecture-review.md` with fresh findings.

### Building or Refactoring the Application
Once `docs/architecture.md` or `docs/architecture-review.md` is approved, start a fresh coding session with Codex, Claude Code, Gemini, or Copilot:
> "Implement Milestone 1 from `docs/architecture.md` and run the verification tests."

Or after an architecture review:
> "Implement Milestone 1 (`REC-1`) from `docs/architecture-review.md` and run the verification checks."

---

## Author

**Mir Islam** — <mislam@mirislam.com>

---

## License

This project is licensed under the Apache License, Version 2.0. See [LICENSE-2.0.txt](./LICENSE-2.0.txt) for the full license text.
