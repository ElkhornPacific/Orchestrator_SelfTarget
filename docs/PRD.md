# PRD: Initiative phase "executing"

*Generated for the initiative in docs/initiatives/initiative-phase-executing/. Output: docs/initiatives/initiative-phase-executing/PRD.md.*

## 1. Executive Summary

When an initiative is branched, phase is set to "branched" and remains so until end-of-task sets it to "tasks-complete." The phase "executing" is never set when the Coordinator starts the first task, so initiative-status.md does not reflect that execution has begun. This initiative adds a step so that when the Coordinator starts the first task on an initiative branch (in workflow Step 2 or workflow-resume), the phase in docs/initiatives/&lt;slug&gt;/initiative-status.md is set to "executing." The initiative lifecycle then accurately reflects planning-complete, branched, executing, tasks-complete, merge-pending, and complete.

## 2. Problem Statement

- **Status does not reflect reality.** Phase "branched" suggests work has not started. Once the first task is being implemented, the phase should indicate "executing" so operators can distinguish ready-to-run from running.
- **Inconsistent lifecycle.** We have planning-complete, branched, tasks-complete, merge-pending, complete. An explicit "executing" phase makes the lifecycle clearer and avoids ambiguity.
- **Optional but clarifying.** The next action is similar whether phase is "branched" or "executing," but setting "executing" when the first task starts avoids ambiguity and supports operators who check initiative-status to see current state.

## 3. Target Users

- **Coordinators** and operators who run the workflow and check initiative-status.md to see whether an initiative is ready to run, in progress, or complete.
- **Orchestrator maintainers** who want the initiative lifecycle phases to accurately reflect actual state (planning, branching, execution, completion, merge).

## 4. Use Cases

- **UC-1:** When the Coordinator begins the first task on an initiative branch (Step 2 Executor or workflow-resume), initiative-status.md is updated to Phase: executing so that anyone reading the status sees that task execution has started.
- **UC-2:** An operator opening initiative-status.md during a run can distinguish "branched" (ready, not yet started) from "executing" (first or subsequent task in progress).
- **UC-3:** The full lifecycle is explicit: planning-complete → branched → executing → tasks-complete → merge-pending → complete.

## 5. User Stories and Acceptance Criteria

- **US-1:** As a Coordinator, I want the phase to be set to "executing" when the first task starts so that initiative-status reflects reality and I do not have to infer state from other artifacts.
- **US-2:** As an operator, I want to open initiative-status.md and see "executing" when work has begun so that I can distinguish ready-to-run from in-progress.
- **US-3:** As a maintainer, I want the lifecycle to be consistent (including an explicit executing phase) so that workflow docs and tooling can reference phases unambiguously.

## 6. Functional Requirements

REQ-001: When the Coordinator starts the first task on an initiative branch (e.g. in workflow Step 2 or in workflow-resume when determining/starting the next task), the Coordinator SHALL update docs/initiatives/&lt;slug&gt;/initiative-status.md to set Phase to "executing" (where &lt;slug&gt; is the current initiative).
REQ-002: The update SHALL occur at the start of the first task execution (before or when the Executor is spawned), not after the task completes, so that status reflects "executing" during the run.
REQ-003: docs/workflow.md and/or docs/workflow-resume.md SHALL document that when starting the first task on an initiative branch, the Coordinator updates initiative-status.md to Phase: executing so that the step is auditable and repeatable.

## 7. Non-Functional Requirements

- The change SHALL be minimal: add or adjust one workflow step (and any script or prompt text) to set Phase: executing; do not change gate semantics, allowlist, or enforcement logic.
- The initiative-status.md file and its location (docs/initiatives/&lt;slug&gt;/initiative-status.md) SHALL remain as today; only the phase value and the moment at which it is set SHALL change.
- Existing phases (planning-complete, branched, tasks-complete, merge-pending, complete) SHALL remain; "executing" is added between "branched" and "tasks-complete."

## 8. Success Metrics / KPIs

- When the Coordinator starts the first task on an initiative branch, initiative-status.md shows Phase: executing for the duration of task execution.
- No regression in workflow gates or dual-review process; the only behavioral change is the phase update and its documentation.

## 9. UX Considerations

- The phase update SHALL be automatic (Coordinator or workflow step), not a manual edit, so that operators are not required to remember to set "executing."
- initiative-status.md SHALL remain the single place to see phase; no new status file or duplicate state.

## 10. Open Questions

- **Where exactly to set the phase:** In workflow.md Step 2 ("When starting the first task of an initiative") we already update phase to "executing" per the current workflow-resume text; this initiative makes that explicit and ensures it is done. If the workflow already contains this step, the initiative may be satisfied by documentation and verification only. Resolve during implementation: confirm whether the update already occurs and, if not, add it.

## 11. Assumptions and Dependencies

- docs/initiatives/&lt;slug&gt;/initiative-status.md exists when an initiative is in phase "branched" (created by initiative_resume.py or planning launcher).
- The Coordinator (or the agent following workflow.md) can determine the current initiative slug (e.g. from workflow-state, branch name, or context) to write to the correct initiative-status.md.
- workflow-resume.md and workflow.md are the authoritative sources for when to update phase; execute.md may reference them.

## 12. Risks and Mitigation Plan

- **Risk:** Phase could be set too early (e.g. at branch creation) or too late (after first task completes), leading to a window where status is wrong.  
  **Mitigation:** REQ-002 specifies "at the start of the first task execution"; implementation should update phase when the Coordinator is about to spawn the Executor for the first task (or at the equivalent moment in workflow-resume).

- **Risk:** Multiple initiatives or branches could cause the wrong initiative-status.md to be updated.  
  **Mitigation:** One initiative at a time is policy; workflow-state and target_repo refer to the current initiative. The Coordinator updates only the current initiative's initiative-status.md (slug from context).

## 13. Tool Role and Architecture Risk Assessment

**Tool classification:** No new external tools. The change affects workflow steps and possibly a small script or prompt text that writes to initiative-status.md. Orchestrator repo docs (workflow.md, workflow-resume.md) and the initiative-status file are the only artifacts.

**System of record:** docs/initiatives/&lt;slug&gt;/initiative-status.md remains the system of record for initiative phase; this initiative does not change SOR, only when "executing" is written.

**High-risk tools:** None.

**Disallowed as SOR:** N/A. No external service or new system of record is introduced.
