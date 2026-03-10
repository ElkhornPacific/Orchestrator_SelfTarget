# Product Requirements Document: Initiative Script Tests

**Initiative:** initiative-script-tests  
**Version:** 1.0  
**Status:** Draft

---

## 1. Executive Summary

This initiative adds an automated test suite for the Orchestrator initiative workflow scripts (`initiative_branch.py`, `initiative_merge.py`, `update_selftarget_from_main.py`) and optionally for `initiative_resume.py` and the planning launcher’s initiative-status output. Tests will run in temporary repositories or with mocks, assert branch creation, file copy, merge, branch deletion, status file updates, and launcher output. The goal is to provide regression safety, document and lock in expected behavior for edge cases, and enable CI to catch breakage before release.

---

## 2. Problem Statement

The initiative lifecycle (planning → branch → execute → merge → update SelfTarget → phase complete) is critical for Orchestrator development. Today there are no automated tests for the scripts that implement it. Changes to these scripts can break the workflow without any test failing. Edge cases (branch already exists, uncommitted changes, merge conflicts, no changes to copy in SelfTarget, malformed initiative-status) are only partially handled and not validated. Launcher and resume behavior (initiative-status.md content and next-action output) is not asserted by tests, so changes to status format or launcher logic can break resume flows without detection.

---

## 3. Target Users

- **Orchestrator maintainers** who change initiative scripts or planning launcher logic and need to avoid regressions.
- **CI pipelines** that must validate the initiative workflow on every change.
- **Contributors** who need clear, executable specifications of how the scripts behave in normal and edge cases.

---

## 4. Use Cases

| ID | Use Case | Actor |
|----|----------|--------|
| UC-1 | Run full test suite for initiative scripts in CI | CI / developer |
| UC-2 | Verify initiative_branch creates branch, copies files, updates workflow-state and initiative-status | Developer / CI |
| UC-3 | Verify initiative_merge merges branch, deletes branch, updates initiative-status to merge-pending | Developer / CI |
| UC-4 | Verify update_selftarget_from_main copies files and optionally commits (no push in test) | Developer / CI |
| UC-5 | Verify launcher produces valid initiative-status content for a given state | Developer / CI |
| UC-6 | Verify initiative_resume (or equivalent) produces expected next-action output for fixture status files | Developer / CI |
| UC-7 | Assert behavior when branch already exists, uncommitted changes, merge conflicts, no changes in SelfTarget | Developer / CI |

---

## 5. User Stories & Acceptance Criteria

- **US-1:** As a maintainer, I run `pytest tests/initiative/` (or equivalent) so that all initiative script tests pass and I know the workflow is intact.
- **US-2:** As a maintainer, I add or change a test so that expected behavior for a new or changed edge case is documented and enforced.
- **US-3:** As CI, I run the test suite on every commit so that regressions in initiative_branch, initiative_merge, or update_selftarget_from_main are caught before merge.

**Acceptance criteria:** Tests exist under `tests/` (e.g. `tests/initiative/`); they run without real git remotes or production repos; happy path and specified edge cases are covered; CI runs the suite.

---

## 6. Functional Requirements

REQ-001: The test suite SHALL include tests for `scripts/initiative_branch.py` that assert branch creation, copy of PRD.md, Architecture.md, Tasks.md to docs/, and creation or update of workflow-state and initiative-status under docs/initiatives/<slug>/.
- **REQ-002:** The test suite SHALL include tests for `scripts/initiative_merge.py` that assert checkout of default branch, pull, merge of initiative branch, branch deletion, and update of initiative-status to phase merge-pending.
- **REQ-003:** The test suite SHALL include tests for `scripts/update_selftarget_from_main.py` that assert file copy from Orchestrator to SelfTarget, optional commit when there are changes, and no push in test environment.
- **REQ-004:** The test suite SHALL run in temporary directories or with mocked git so that no real remotes or persistent repos are required.
- **REQ-005:** The test suite SHALL assert behavior when the initiative branch already exists (initiative_branch), when there are uncommitted changes on the branch (initiative_merge), and when there are no changes to copy in SelfTarget (update_selftarget_from_main), as specified by the implementation.
- **REQ-006:** The test suite SHALL include tests or assertions for the planning launcher’s initiative-status output (phase, next action, execution state section) when run in initiative mode, using fixture initiative-status files where appropriate.
- **REQ-007:** The test suite MAY include tests for initiative_resume (or equivalent) that assert printed next action given fixture initiative-status.md content.
- **REQ-008:** Functional requirements SHALL use requirement IDs REQ-001 through REQ-NNN at line start for Orchestrator drift detection compatibility.

---

## 7. Non-Functional Requirements

- **NFR-1:** Tests SHALL complete in a reasonable time (e.g. under a few minutes for the full initiative suite) and SHALL not require network or external services.
- **NFR-2:** Tests SHALL be deterministic and SHALL not depend on system timezone or locale in a way that causes flakiness.
- **NFR-3:** Test code SHALL live under `tests/` (e.g. `tests/initiative/`) and follow existing project test conventions (e.g. pytest).

---

## 8. Success Metrics / KPIs

- All new tests pass in CI on every run.
- No regressions in initiative workflow after changes to initiative_branch, initiative_merge, or update_selftarget_from_main without a corresponding test update or failure.
- Edge cases documented in INITIATIVE-EVALUATION.md (branch already exists, uncommitted changes, merge conflicts, no changes in SelfTarget) have corresponding test coverage.

---

## 9. UX Considerations

Not applicable (internal tooling and tests; no end-user UI).

---

## 10. Open Questions

- Whether to use tempfile + real git in temp dirs vs. mocks for git commands (trade-off: realism vs. speed and simplicity).
- Scope of merge-conflict tests (e.g. unit test with mocked conflict vs. integration-style test).
- Exact placement: `tests/initiative/` vs. `tests/` with prefix `test_initiative_*.py`.

---

## 11. Assumptions & Dependencies

- **Assumptions:** Existing pytest (or equivalent) setup in the repo; git available in test environment; initiative scripts are invocable as subprocess or importable for unit tests.
- **Dependencies:** None on external services; may depend on structure of docs/initiatives/ and scripts/ as defined in the codebase.

---

## 12. Risks & Mitigation Plan

| Risk | Mitigation |
|------|-------------|
| Tests flaky due to git timing or filesystem | Use isolated temp dirs per test; avoid shared state; consider --no-wait or short timeouts only where necessary. |
| Merge or branch tests require complex setup | Prefer focused unit tests with mocks where integration tests are too heavy; document one or two key integration scenarios. |
| Launcher tests depend on many files | Use fixture initiative-status.md and minimal docs layout; avoid full repo bootstrap where possible. |

---

## 13. Tool Role & Architecture Risk Assessment

### Tool Classification by Role

- **Git (local):** Processing / Compute — used to create branches, merge, commit. Not a system of record for product data; used for version control of artifacts.
- **Test runner (pytest):** Processing / Compute — executes tests. No system-of-record role.
- **Temp directory / filesystem:** Retrieval / Storage — used only for test isolation. Ephemeral; not a system of record.

### System of Record

- No product data domain is stored by this initiative. Tests validate behavior of scripts; the only “data” is test fixtures and expected output.

### High-Risk Tools

- None. Git and filesystem are standard and stable for this use.

### Disallowed Systems of Record

- Not applicable; no external SaaS or storage is introduced.

---

*This PRD was generated from the initiative description in docs/initiatives/initiative-script-tests/description.md, following create-prd.md. Output location: docs/initiatives/initiative-script-tests/PRD.md.*
