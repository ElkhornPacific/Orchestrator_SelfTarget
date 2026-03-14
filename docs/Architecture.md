# Architecture: Initiative phase "executing"

This document defines the architecture elements for the initiative-phase-executing initiative. Each ARC implements one or more requirements from the PRD; tasks reference these ARCs for drift coverage.

ARC-001: Workflow documentation of phase update. Implements REQ-003. docs/workflow.md and docs/workflow-resume.md SHALL explicitly state that when the Coordinator starts the first task on an initiative branch (phase branched), the Coordinator updates docs/initiatives/&lt;slug&gt;/initiative-status.md to Phase: executing so that the step is auditable and repeatable.

ARC-002: Phase update at start of first task and runbook instruction. Implements REQ-001, REQ-002. The Coordinator SHALL update initiative-status.md to Phase: executing at the start of the first task execution (before or when the Executor is spawned). docs/initiatives/_template/execute.md (and initiative execute.md where used) SHALL include an explicit instruction that when starting the first task (phase branched), the Coordinator MUST set initiative-status.md to Phase: executing so that agents following the runbook perform the update.

ARC-003: Verification of phase-update step. Confirms REQ-001, REQ-002, REQ-003 are satisfied. The initiative SHALL verify that workflow.md Step 2, workflow-resume.md (step 3 for branched/executing), and execute.md (template or initiative) contain the phase-update instruction and that there is no regression in workflow gates.
