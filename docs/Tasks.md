# Tasks: Initiative phase "executing"

Tasks are ordered for execution. Each task references one or more ARC IDs for drift coverage.

TASK-001: Audit docs/workflow.md (Step 2) and docs/workflow-resume.md (step 3 for branched/executing). Confirm both explicitly state that when starting the first task on an initiative branch, the Coordinator updates initiative-status.md to Phase: executing. If either doc is ambiguous or omits "executing" or "first task," add or tighten one sentence to make the requirement explicit. Preserve REQ-003. References ARC-001.

TASK-002: Ensure docs/initiatives/_template/execute.md includes an explicit instruction that when the Coordinator starts the first task (phase still branched), they MUST update docs/initiatives/&lt;slug&gt;/initiative-status.md to Phase: executing before spawning the Executor. If the template already states this (e.g. via "as the workflow specifies"), add one explicit bullet under the "Follow the workflow" step so agents cannot miss it. References ARC-002.

TASK-003: Verify that (1) workflow.md Step 2 contains the phase-update bullet, (2) workflow-resume.md step 3 contains the phase-update instruction for when starting the first task (phase still branched), and (3) execute.md template instructs the Coordinator to set Phase: executing when starting the first task. Confirm no regression in workflow gates. Optionally add a short verification note in docs/initiatives/initiative-phase-executing/. References ARC-003.
