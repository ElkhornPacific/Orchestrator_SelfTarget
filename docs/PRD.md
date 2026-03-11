# Product Requirements Document: Target Identity Lock

**Initiative:** target-identity-lock  
**Version:** 1.0  
**Status:** Draft

---

## 1. Executive Summary

Target Identity Lock (wrong-repo protection) ensures the Orchestrator and any execution agent (e.g. Cursor) can only modify, commit, or publish the **intended** target repository chosen at the start of a run. At run start the Orchestrator computes and records a stable identity for the selected target (e.g. normalized absolute path, git root, optional remote URL or HEAD). Before every write operation, Cursor instruction, and commit/publish action, it re-validates that the target still matches this identity. On mismatch, the run fails closed: stop immediately, mark run FAIL, record a violation (e.g. target_identity_mismatch). All identity checks and results are recorded in the run bundle for audit. This initiative implements the roadmap safety feature described in docs/orchestrator-roadmap.md and enables safe scaling beyond SelfTarget and TestTarget.

---

## 2. Problem Statement

In a multi-repo setup (Orchestrator, SelfTarget, TestTarget, and eventually external repos), similarly named paths and working directories exist across repos. A bug or wrong cwd could cause edits, commits, or pushes to the wrong repo. Today run reports show what was executed but do not explicitly check or record the identity of the target repo for each action, so there is no enforceable guarantee that "we only ever touched the intended repo." Git operations depend on current working directory and remotes; a script or agent in the wrong cwd could commit or push the wrong repo. As more target repositories are added, the chance of confusion increases. Target Identity Lock is the primary mechanism in the roadmap to make multi-repo operation safe.

---

## 3. Target Users

- **Orchestrator operators** who run the orchestrator against one or more target repos and must ensure no accidental modification of the wrong repository.
- **Auditors and supervisors** who need to verify that every mutating action was bound to the intended target.
- **Maintainers** who add or change target repos and need a single, enforceable safety layer to prevent wrong-repo edits and publish.

---

## 4. Use Cases

| ID | Use Case | Actor |
|----|----------|--------|
| UC-1 | At run start, record a stable target identity (path, git root, optional remote/HEAD) for the selected target repo | Orchestrator |
| UC-2 | Before every write to the target repo, verify current target matches the recorded identity; on mismatch, FAIL and record violation | Orchestrator |
| UC-3 | Before every Cursor instruction that modifies the target, verify target identity and include identity in the instruction; on mismatch or missing echo, FAIL | Orchestrator / Coordinator |
| UC-4 | Before every commit or publish action in the target repo, re-validate target identity and cwd; on mismatch, do not publish | Orchestrator / Coordinator |
| UC-5 | Record target identity and verification results for each gate in the run bundle so auditors can confirm no boundary crossing | Orchestrator |
| UC-6 | Scale to additional target repos (beyond SelfTarget and TestTarget) with a single, consistent wrong-repo protection mechanism | Operators |

---

## 5. User Stories & Acceptance Criteria

- **US-1:** As an operator, I run the Orchestrator against a target repo so that every write, Cursor call, and commit/publish is gated on a recorded target identity and any mismatch causes immediate FAIL.
- **US-2:** As an auditor, I read the run bundle so that I can confirm the orchestrator never crossed repository boundaries (identity and verification results are present for each action).
- **US-3:** As a maintainer, I add a new target repo so that Target Identity Lock applies without additional per-repo configuration beyond selecting the target at run start.

**Acceptance criteria:** Run report (or run bundle) includes target identity and verification results; all write paths, Cursor invocations, and commit/publish paths re-validate identity; violation type (e.g. target_identity_mismatch) is defined and recorded on FAIL.

---

## 6. Functional Requirements

REQ-001: At the start of each run, the Orchestrator SHALL compute and record a stable target identity for the selected target repository (e.g. normalized absolute path, git repository root).
REQ-002: The Orchestrator SHALL support optional inclusion of git remote URL and/or current HEAD commit in the target identity for stronger binding where available.
REQ-003: Before any write operation to the target repository (including run report, baseline capture, step artifacts), the Orchestrator SHALL verify that the target being written to matches the recorded identity; on mismatch, SHALL stop immediately, set overall_status to FAIL, and record a violation (e.g. target_identity_mismatch).
REQ-004: Before any instruction sent to Cursor that may modify the target repo, the Coordinator/Orchestrator SHALL include the target identity (or a hash thereof) and allowed write paths; Cursor output SHALL be checked for identity echo where applicable; on mismatch or missing echo, SHALL treat as FAIL.
REQ-005: Before any commit or publish action in the target repo, the Orchestrator or Coordinator SHALL re-validate that the target identity still matches and the working directory is inside the target repo root; on failure, SHALL not perform the commit or push.
REQ-006: The run report or run bundle SHALL include the recorded target identity (e.g. path, identity hash, optional remote/HEAD) and SHALL include verification results (e.g. passed/failed, violation if any) for each gate that performed an identity check.
REQ-007: Violations SHALL be recorded in a deterministic, machine-readable form (e.g. in run report) so that failure type target_identity_mismatch can be used by failure policy and drift/audit tooling.
REQ-008: Functional requirements SHALL use requirement IDs REQ-001 through REQ-NNN at line start for Orchestrator drift detection compatibility.

---

## 7. Non-Functional Requirements

- **NFR-1:** Identity computation and verification SHALL add minimal latency to the pipeline; identity SHALL be computed once at run start and re-validation SHALL be a fast check (e.g. path comparison, optional hash).
- **NFR-2:** Identity and verification data in the run bundle SHALL be sufficient for audit without requiring access to live repos (e.g. path, hash, timestamp).
- **NFR-3:** The feature SHALL not change the enforcement pipeline order defined in docs/enforcement-pipeline.md except by adding identity capture at start and identity checks at existing write/commit/publish boundaries.

---

## 8. Success Metrics / KPIs

- Zero incidents of wrong-repo modification, commit, or push when the feature is enabled and correctly configured.
- Run bundles consistently include target identity and per-action verification results when Target Identity Lock is active.
- Failure type target_identity_mismatch is observable in run reports and can be used to halt automation and alert operators.

---

## 9. UX Considerations

- Operators must understand that a run can FAIL with target_identity_mismatch if cwd, path, or repo state changes unexpectedly; documentation SHALL explain how to fix (e.g. re-run with correct target, fix cwd).
- Run report and logs SHALL clearly indicate when a failure was due to identity mismatch versus other failure types.

---

## 10. Open Questions

- Whether to make Target Identity Lock always-on for all runs or opt-in via contract or CLI flag (roadmap implies always-on for safety).
- Exact identity inputs: minimum set (path + git root) vs. optional remote URL and HEAD for stricter binding.
- How Cursor instructions "echo" identity (e.g. require a specific line in output, or rely only on pre-instruction checks).

---

## 11. Assumptions & Dependencies

- **Assumptions:** Target repo is selected at run start via existing mechanism (e.g. --repo); git is available when optional remote/HEAD is used; run report and run bundle schema can be extended with identity and verification fields.
- **Dependencies:** docs/orchestrator-roadmap.md (Target Identity Lock section); docs/enforcement-pipeline.md (pipeline order); existing write paths and commit/publish touchpoints in orchestrator.py and workflow.

---

## 12. Risks & Mitigation Plan

| Risk | Mitigation |
|------|------------|
| Identity check false positives (e.g. path normalization differences) | Use normalized paths; document normalization rules; allow optional lenient mode for known-safe environments if needed. |
| Cursor or external scripts change cwd | Re-validate immediately before every mutating action; do not rely on cwd from earlier in the run. |
| Run bundle size growth | Store identity once and reference by key; store only verification result (pass/fail + violation type) per gate, not full payload. |

---

## 13. Tool Role & Architecture Risk Assessment

### Tool Classification by Role

- **Orchestrator (orchestrator.py):** Processing / Compute — executes enforcement pipeline and gates; records and checks target identity. Not a system of record for target repo content; it is the authority for run report and run bundle.
- **Git (local):** Processing / Compute — used for commit/publish in target repo; also used to obtain git root, remote URL, HEAD for identity. Not a system of record for product data.
- **Cursor / execution agent:** Authoring Surface (for target repo edits) — receives instructions bound to target identity; must not modify repos other than the intended target.

### System of Record

- **Target repository:** Authoritative for its own docs/GuardContract.json, PRD, Architecture, Tasks, and source code. Target Identity Lock does not change that; it only ensures the Orchestrator and agents act against the correct target.
- **Run report / run bundle:** Authoritative for what the Orchestrator did and which target identity was used; stored in Orchestrator repo or target repo per existing policy (e.g. run-report path).

### High-Risk Tools

- None new. Git and Cursor are existing dependencies; identity lock adds checks around their use rather than introducing new tools.

### Disallowed Systems of Record

- No external SaaS or storage is introduced as a system of record. Target repos remain the only system of record for their own artifacts.

---

*This PRD was generated from the initiative description in docs/initiatives/target-identity-lock/description.md, following create-prd.md. Output location: docs/initiatives/target-identity-lock/PRD.md.*
