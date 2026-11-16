---
description: Run the SDArch 3-agent, memory-isolated planning, requirements, and architecture workflow for a project goal.
argument-hint: "<your project or feature idea>"
---

Run the `sdarch-workflow` skill (`skills/sdarch-workflow/SKILL.md`) for the following user goal:

$ARGUMENTS

Strictly enforce zero shared memory between subagents: each invocation of `planning-agent`, `architecture-agent`, and `review-agent` must run in a fresh context with only the relevant `docs/*.md` file paths passed in.
