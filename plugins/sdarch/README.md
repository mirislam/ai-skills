# SDArch — Document-Driven Multi-Agent Software Design Plugin

**SDArch** is a cross-platform plugin for daily software development that turns a brief, rough idea into a thoroughly vetted, coding-agent-ready system architecture using **three independent, memory-isolated agents**:

1. **Planning Agent** (`planning-agent`) — Expands your raw goal into a rich `docs/inspiration.md`, then (in a separate, fresh run) translates it into testable `docs/requirements.md`.
2. **Design & Architecture Agent** (`architecture-agent`) — Reads `docs/inspiration.md` and `docs/requirements.md` to produce a concrete `docs/architecture.md` ready to hand off to any coding agent (Codex, Claude Code, Gemini, or GitHub Copilot).
3. **Review Agent** (`review-agent`) — Acts as an independent quality gate after every phase, auditing alignment and completeness in `docs/review-feedback.md` and sending work back for revision when gaps are found.

---

## Why We Built SDArch

I created SDArch because I was getting tired of constantly prompting agents to ask clarifying questions, challenge their own assumptions, and re-review the work they had already done—all via manual prompts. Doing that by hand did not scale. This small, compact plugin automates that discipline so everything stays aligned without bloating the coding agents' context windows.

When developers jump straight from a 2-sentence prompt into code generation, two things go wrong:
1. **Context Drift & Sycophancy:** A single long-running agent remembers its own shortcuts and rationalizes missing edge cases.
2. **Underspecified Handoffs:** Coding agents write brittle code when requirements and module boundaries are left implicit.

**SDArch solves this with strict memory isolation and document-driven state:**
- **Zero Shared Memory:** Every agent invocation starts in a fresh context with **no memory** of previous agent runs. It can only judge progress and make decisions from the markdown documents on disk.
- **Built-in Adversarial Review:** Because the Review Agent has no memory of *why* the Planning or Architecture Agent made a choice, it evaluates the documents strictly on what is written—catching ambiguity before a single line of application code is written.
- **Coding-Agent Ready Output:** The final `docs/architecture.md` contains an exact tech stack, directory layout, data schemas, API/CLI/UI contracts, requirement traceability matrix, and ordered build milestones.

---

## How the Workflow Works

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

### Generated Artifacts (`docs/`)
- `docs/inspiration.md` — Expanded product vision, personas, user journeys, edge cases, inferred assumptions, and non-goals.
- `docs/requirements.md` — Numbered functional (`FR-*`) and non-functional (`NFR-*`) requirements with acceptance criteria.
- `docs/architecture.md` — Concrete system design, file tree, schemas, interfaces, traceability matrix, and implementation milestones.
- `docs/review-feedback.md` — Latest Review Agent audit (`APPROVED` or `REVISION_REQUIRED`) with actionable blocking issues.

---

## Repository Structure (Multi-Platform)

SDArch uses a single shared core (`skills/` and `agents/`) with lightweight manifests/wrappers for each agentic platform:

```text
sdarch/
├── plugin.json                        # Antigravity plugin manifest
├── .claude-plugin/plugin.json         # Claude Code plugin manifest
├── AGENTS.md                          # Universal instructions (Codex, Antigravity, etc.)
├── commands/sdarch.md                 # /sdarch slash command (Claude Code)
├── agents/                            # Canonical isolated subagent definitions
│   ├── planning-agent.md
│   ├── architecture-agent.md
│   └── review-agent.md
├── skills/sdarch-workflow/            # Open Agent Skills standard
│   ├── SKILL.md                       # Pipeline orchestrator
│   └── references/                    # Document templates & review rubric
│       ├── inspiration-template.md
│       ├── requirements-template.md
│       ├── architecture-template.md
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

### Starting a New Feature or App
Prompt your agent with your idea—even if it is brief:
> "Use `sdarch-workflow` to plan and architect a local-first CLI bookmark manager with full-text search and browser sync."

In Claude Code, you can also use the slash command:
```text
/sdarch A local-first CLI bookmark manager with full-text search and browser sync
```

The orchestrator will run Phases 1 → 3 automatically, looping with the Review Agent (up to 3 iterations per phase) until `docs/architecture.md` is approved.

### Resuming or Re-running a Phase
Because state lives purely on disk in `docs/`:
- Edit `docs/inspiration.md`, delete `docs/requirements.md` and `docs/architecture.md`, and re-run `sdarch-workflow` to regenerate downstream docs.
- Or jump straight to Phase 3 if you already wrote `docs/inspiration.md` and `docs/requirements.md`.

### Building the Application
Once `docs/architecture.md` is approved, start a fresh coding session with Codex, Claude Code, Gemini, or Copilot:
> "Implement Milestone 1 from `docs/architecture.md` and run the verification tests."

---

## Author

**Mir Islam** — <mislam@mirislam.com>
