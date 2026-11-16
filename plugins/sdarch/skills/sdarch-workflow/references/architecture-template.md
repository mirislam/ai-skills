# Architecture Document Template (`docs/architecture.md`)

Use this structure when creating `docs/architecture.md`. The output must be concrete enough for a coding agent (Codex, Claude Code, Gemini, Copilot) to implement end-to-end without guessing.

```markdown
# <Project / Feature Title> — System Architecture

## 1. Architectural Overview & Tech Stack
- **Summary:** <High-level system design>
- **Tech Stack:**
  - Language / Runtime: <Exact version/choice + rationale>
  - Frameworks / Libraries: <Exact packages + rationale>
  - Storage / State: <Database, files, or in-memory state>
  - Testing: <Test framework & tooling>

## 2. Repository & Directory Layout
```text
<project-root>/
├── src/
│   └── ...        # Responsibility of each file/module
├── tests/
│   └── ...        # Unit & integration test layout
└── ...
```

## 3. System Components & Data Flow
- **Component A (`path/to/file`):** <Responsibilities, public interfaces, dependencies>
- **Component B (`path/to/file`):** <Responsibilities, public interfaces, dependencies>
- **Data Flow:** <Step-by-step flow for primary operations>

## 4. Data Models & Schemas
- Define exact types, fields, constraints, and relationships (JSON schemas, SQL DDL, TypeScript interfaces, or Python dataclasses).

## 5. API / CLI / UI Contracts
- Exact endpoints, CLI flags/commands, or component props, including request/response shapes and error codes.

## 6. Security, Error Handling & Observability
- Input validation, auth boundaries, error propagation strategy, and logging/diagnostics.

## 7. Requirement Traceability Matrix
| Requirement ID | Implementing Component(s) | Verification Method |
| :--- | :--- | :--- |
| FR-1 | `<module/file>` | `<unit/integration test>` |
| NFR-1 | `<module/file>` | `<benchmark/check>` |

## 8. Implementation Plan (For Coding Agents)
Ordered milestones so a coding agent can build and verify incrementally:
1. **Milestone 1 — Scaffolding & Core Models:** <Files to create, command to verify>
2. **Milestone 2 — Core Logic & Interfaces:** <Files to create, tests to run>
3. **Milestone 3 — Integration, Edge Cases & Polish:** <End-to-end verification>
```
