# Tasks: One initiative at a time

Tasks are ordered for execution. Each task references one or more ARC IDs for drift coverage.

TASK-001: Add the primary "one initiative at a time" statement to docs/initiatives/README.md. Statement SHALL state that only one initiative should be active at a time, that docs/workflow-state.md and target_repo refer to the current initiative, and that parallel initiatives or context-switching without completing or abandoning the current one are discouraged. Place after the intro or in a short "One initiative at a time" subsection. References ARC-001.

TASK-002: Add the same primary statement to docs/initiatives/RESUME.md using identical or near-identical wording to README.md. Optionally add one sentence that resuming implies the current initiative is the one reflected in workflow-state and target_repo. References ARC-001.

TASK-003: Verify both README.md and RESUME.md contain the policy; confirm no script or workflow behavior changes. Optionally grep for "one initiative" (or equivalent) in both files. References ARC-001.
