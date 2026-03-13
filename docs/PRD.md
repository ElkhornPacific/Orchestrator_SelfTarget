# PRD: One initiative at a time

*Generated for the initiative in docs/initiatives/one-initiative-at-a-time/. Output: docs/initiatives/one-initiative-at-a-time/PRD.md.*

## 1. Executive Summary

The Orchestrator workflow uses a single docs/workflow-state.md and a single target_repo per run. The design is one initiative at a time: only one initiative can be "current" in workflow-state. Today this constraint is not stated in the canonical initiative docs, so operators or new contributors may assume they can run multiple initiatives in parallel or switch contexts freely, leading to undefined behavior (wrong merge order, wrong context). This initiative documents the policy explicitly in docs/initiatives/README.md or docs/initiatives/RESUME.md so that only one initiative should be active at a time and workflow-state and target_repo always refer to the current initiative.

## 2. Problem Statement

- **Undefined behavior for parallel initiatives.** Someone could branch two initiatives, switch between them, and update workflow-state for each. Which branch and which task list are "current" becomes ambiguous; merge and SelfTarget steps could be applied in the wrong order or to the wrong context.
- **No stated design constraint.** The code and docs do not state that the design is single-initiative, so new contributors may assume parallel or context-switching is supported.
- **Resume clarity.** When resuming, it should be explicit that the state file and target_repo refer to one initiative. Documenting "one initiative at a time" sets expectations for operators.

## 3. Target Users

- **Coordinators** and operators running the Orchestrator workflow who need to know that workflow-state and target_repo refer to a single current initiative.
- **New contributors** who need to understand that the design is one initiative at a time.
- **Orchestrator maintainers** who want to avoid undefined behavior and wrong-context merges or SelfTarget updates.

## 4. Use Cases

- **UC-1:** When starting or resuming work, the operator reads README or RESUME and sees that only one initiative should be active at a time and that workflow-state and target_repo refer to the current initiative.
- **UC-2:** Before branching a second initiative, the operator is informed by documentation that the design expects one initiative at a time, so parallel runs are discouraged.
- **UC-3:** After a merge or SelfTarget update, there is no ambiguity about which initiative was "current" because the docs state that workflow-state and target_repo always refer to the current (single) initiative.

## 5. User Stories and Acceptance Criteria

- **US-1:** As a Coordinator, I want the initiative docs to state clearly that only one initiative should be active at a time so that I do not accidentally treat workflow-state as applicable to multiple branches.
- **US-2:** As a new contributor, I want to see the single-initiative design stated in README or RESUME so that I do not assume parallel initiatives are supported.
- **US-3:** As a maintainer, I want the policy documented so that resume behavior and merge/SelfTarget steps are unambiguous.

## 6. Functional Requirements

REQ-001: docs/initiatives/README.md or docs/initiatives/RESUME.md SHALL state explicitly that only one initiative should be active at a time.
REQ-002: docs/initiatives/README.md or docs/initiatives/RESUME.md SHALL state that docs/workflow-state.md and target_repo always refer to the current initiative.
REQ-003: The documentation SHALL discourage running multiple initiatives in parallel or switching context between initiatives without completing or abandoning the current one (e.g. by stating that the design is single-initiative).

## 7. Non-Functional Requirements

- Changes SHALL be documentation-only; no changes to scripts or runtime behavior.
- Wording SHALL be concise so that the constraint is discoverable in README or RESUME without reading the full workflow.
- The existing workflow behavior (single workflow-state, single target_repo) SHALL not change; only the explicit statement of the design constraint SHALL be added.

## 8. Success Metrics / KPIs

- Operators and contributors can find the "one initiative at a time" policy in README or RESUME.
- No new reports of ambiguity about which initiative is "current" when resuming or when performing merge/SelfTarget steps (observable via session-changelog or feedback).

## 9. UX Considerations

- Place the statement where operators and contributors already look: README.md or RESUME.md under docs/initiatives/.
- Keep the statement short (one or two sentences) so it does not add friction; link to workflow-state or workflow.md if more detail is needed.

## 10. Open Questions

- Resolved: Both README.md and RESUME.md SHALL contain the primary statement (one initiative at a time; workflow-state and target_repo refer to the current initiative). This ensures the policy is visible from the initiative index and from the one-page resume flow without cross-referencing.

## 11. Assumptions and Dependencies

- docs/workflow-state.md and the single target_repo per run will continue to be the design; this initiative does not propose changing that, only documenting it.
- docs/initiatives/README.md and docs/initiatives/RESUME.md exist and are the right place for operator-facing policy.

## 12. Risks and Mitigation Plan

- **Risk:** Wording could be too strong ("must never") and discourage legitimate workflows (e.g. abandoning one initiative and starting another).  
  **Mitigation:** Use "should" or "design is one initiative at a time" and "workflow-state and target_repo refer to the current initiative"; allow for explicit context switch (e.g. abandon or complete current initiative first) rather than implying a hard technical lock.

- **Risk:** Duplicate wording in README and RESUME could get out of sync.  
  **Mitigation:** State the policy in one place (e.g. RESUME.md) and reference it from README.md, or use a single short sentence that is identical in both.

## 13. Tool Role and Architecture Risk Assessment

**Tool classification:** No new external tools or services. This initiative adds documentation only; the only "tools" are the existing docs (README.md, RESUME.md) and workflow-state.md.

**System of record:** docs/workflow-state.md remains the single canonical state file for the workflow; this initiative does not change that. The initiative docs (README, RESUME) are authoritative for operator-facing policy.

**High-risk tools:** None.

**Disallowed as SOR:** N/A; no external SaaS or new systems. Orchestrator repo docs remain the source of truth for the one-initiative-at-a-time policy.
