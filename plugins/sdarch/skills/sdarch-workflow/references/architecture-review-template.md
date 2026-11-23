# Architecture Review & Recommendations Template (`docs/architecture-review.md`)

Use this structure when analyzing an existing codebase (and any `arch*.md` documentation) to produce `docs/architecture-review.md`. The output must give human engineers a clear qualitative assessment **and** give coding agents (Codex, Claude Code, Gemini, Copilot) concrete, safe, verifiable refactoring milestones.

```markdown
# <Project / Subsystem Title> — Architecture Review & Recommendations

- **Scope Analyzed:** `<Full repository | path/to/subsystem>`
- **Architecture Docs Reviewed:** `<Comma-separated paths to discovered arch*.md files, or "None found">`
- **Supporting Docs Reviewed:** `<docs/requirements.md, docs/inspiration.md, README.md, etc., or "None">`
- **Overall Health Verdict:** `<Sound | Needs Targeted Improvements | Significant Refactoring Recommended>`

## 1. Executive Summary
- **Architectural Fitness:** <2–4 sentences assessing whether the chosen architecture fits the system's problem domain, scale, and constraints.>
- **Key Strengths:** <Top 2–3 architectural decisions working well today.>
- **Primary Risks / Bottlenecks:** <Top 2–3 structural weaknesses or drift issues.>
- **Highest-Leverage Improvements:** <Top 2–3 recommended actions (`REC-*`) and expected impact.>

## 2. As-Is Architecture Overview
- **Observed Tech Stack:** <Languages, runtimes, frameworks, storage, messaging, build/test tooling observed in the repo.>
- **Architectural Style & Topology:** <e.g., Layered monolith, hexagonal/clean architecture, event-driven pipeline, microservices, CLI plugin, etc.>
- **Core Modules & Entry Points:**
  - `<path/to/module-a>` — <Observed responsibility and key dependencies>
  - `<path/to/module-b>` — <Observed responsibility and key dependencies>
- **Primary Data & Control Flow:** <Concise step-by-step trace of the main execution path(s) grounded in real files/symbols.>

## 3. Documented vs. Implemented Architecture (`arch*.md` Analysis)
*(If one or more `arch*.md` files were found, complete both subsections below. If none were found, state that no architecture doc exists and recommend whether creating one is worthwhile.)*

### 3.1 Assessment of Existing Architecture Documentation
- **Clarity & Completeness:** <Does `arch*.md` clearly specify boundaries, data models, contracts, and rationale?>
- **Design Soundness:** <Qualitative critique of the documented design itself, independent of implementation drift.>

### 3.2 Spec-vs-Code Drift Analysis
| Area / Documented Design | Actual Code Reality (File Paths) | Drift Severity | Assessment |
| :--- | :--- | :--- | :--- |
| <Documented component/rule> | `<actual/file/path>` — <What code actually does> | High / Medium / Low | <Whether code should align to doc or doc should be updated> |

## 4. Qualitative Architecture Assessment
Evaluate each dimension qualitatively (`Strong`, `Adequate`, `Weak`, or `Critical`) with concrete file-path evidence:

| Dimension | Rating | Evidence & Analysis |
| :--- | :--- | :--- |
| **Pattern & Complexity Fit** | <Rating> | <Is the architecture right-sized, over-engineered, or under-structured? Cite files.> |
| **Modularity, Coupling & Cohesion** | <Rating> | <Module boundaries, circular dependencies, leaky abstractions, god modules. Cite files.> |
| **Data Modeling & State Management** | <Rating> | <Schemas, state ownership, persistence boundaries, concurrency/consistency. Cite files.> |
| **API / Interface & Contract Design** | <Rating> | <Internal/external interfaces, typing/validation, versioning, error contracts. Cite files.> |
| **Scalability, Performance & Reliability** | <Rating> | <Bottlenecks, failure modes, retries/timeouts, resource limits. Cite files.> |
| **Security & Error Handling** | <Rating> | <Auth/trust boundaries, input validation, secret handling, error propagation, observability. Cite files.> |
| **Testability & Maintainability** | <Rating> | <Test architecture, dependency isolation, onboarding friction, build/test feedback loops. Cite files.> |

### What Is Working Well (Preserve During Refactoring)
- <Good architectural pattern or invariant to keep intact (`path/to/file`)>

## 5. Prioritized Improvement Recommendations
Order recommendations from highest to lowest priority. Every recommendation must have a unique ID (`REC-1`, `REC-2`, ...).

### REC-1: <Short Actionable Title>
- **Priority / Effort / Risk:** `<Critical | High | Medium | Low>` Priority | `<Small | Medium | Large>` Effort | `<Low | Medium | High>` Risk
- **Affected Files / Modules:** `<exact/path/1>`, `<exact/path/2>`
- **Current State & Issue:** <Concrete structural problem observed in the codebase (and/or `arch*.md`).>
- **Recommended Change:** <Specific target design, boundary, interface, or pattern to adopt.>
- **Trade-offs & Caveats:** <Costs, migration considerations, or edge cases to watch out for.>
- **Acceptance Criteria:**
  - <Objective, verifiable condition proving `REC-1` is complete>

### REC-2: <Short Actionable Title>
- ...

## 6. Implementation Roadmap (For Coding Agents)
Ordered, incremental milestones so a developer or coding agent (Codex, Claude Code, Gemini, Copilot) can apply changes safely and verify each step:

1. **Milestone 1 — <Short Name> (Addresses: `REC-1`, ...)**
   - **Target Files:** `<files to create, modify, or remove>`
   - **Steps:** <Concrete, unambiguous implementation instructions>
   - **Verification:** `<Exact build, lint, test, or check commands to run>`
2. **Milestone 2 — <Short Name> (Addresses: `REC-2`, ...)**
   - **Target Files:** `<files to create, modify, or remove>`
   - **Steps:** <Concrete implementation instructions>
   - **Verification:** `<Exact commands to run>`

## 7. Recommendation Traceability Matrix
| Recommendation ID | Priority | Target Files / Modules | Milestone | Verification Method |
| :--- | :--- | :--- | :--- | :--- |
| REC-1 | High | `<path/to/file>` | Milestone 1 | `<unit/integration test or check>` |
| REC-2 | Medium | `<path/to/file>` | Milestone 2 | `<test or check>` |
```
