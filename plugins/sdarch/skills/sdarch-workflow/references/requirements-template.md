# Requirements Document Template (`docs/requirements.md`)

Use this structure when translating `docs/inspiration.md` into `docs/requirements.md`. Every requirement must have a unique ID and testable acceptance criteria.

```markdown
# <Project / Feature Title> — Requirements Specification

## 1. Purpose & Scope
- **Source Vision:** Derived from `docs/inspiration.md`
- **In Scope:** <Concise summary>
- **Out of Scope:** <Explicit exclusions carried over from non-goals>

## 2. User Stories
- **US-1:** As a <persona>, I want to <action> so that <outcome>. *(Maps to: Inspiration §3)*

## 3. Functional Requirements
| ID | Title | Description | Acceptance Criteria | Priority |
| :--- | :--- | :--- | :--- | :--- |
| FR-1 | <Short name> | <Exact system behavior> | <Given/When/Then or verifiable check> | Must |
| FR-2 | <Short name> | <Exact system behavior> | <Verifiable check> | Must |

## 4. Non-Functional Requirements
| ID | Category | Requirement | Target / Metric |
| :--- | :--- | :--- | :--- |
| NFR-1 | Performance | <Latency, throughput, or resource limit> | <Measurable target> |
| NFR-2 | Security | <Auth, input validation, data protection> | <Verifiable control> |
| NFR-3 | Reliability | <Error handling, persistence, recovery> | <Verifiable behavior> |

## 5. Data & Integration Boundaries
- **Core Entities:** <High-level data concepts without locking implementation prematurely>
- **External Integrations:** <APIs, filesystems, CLI tools, or services>

## 6. Assumptions & Open Architectural Questions
- <Questions for the Architecture Agent to resolve concretely>
```
