# Tasks — Initiative Script Tests (Orchestrator)

TASK-001: Create tests/initiative/ and test harness. Add tests/initiative/ directory (or agreed placement). Implement shared helpers: temp repo creation (git init, optional initial commit), path to docs/initiatives/<slug>/, path to scripts/. Follow existing project test conventions (pytest). References ARC-001.

TASK-002: Add initiative_branch tests. Implement tests for scripts/initiative_branch.py: assert branch creation; copy of PRD.md, Architecture.md, Tasks.md to docs/; creation or update of workflow-state and initiative-status under docs/initiatives/<slug>/. Add edge-case test: branch already exists (assert script behavior per current implementation). References ARC-002.

TASK-003: Add initiative_merge tests. Implement tests for scripts/initiative_merge.py: assert checkout of default branch, pull, merge of initiative branch, branch deletion, initiative-status updated to merge-pending. Add edge-case test: uncommitted changes on branch (script exits with message; no checkout/merge). Use temp repo; no real remotes (pull may be no-op or mocked). References ARC-003.

TASK-004: Add update_selftarget_from_main tests. Implement tests using two temp dirs (Orchestrator and SelfTarget): assert file copy from Orchestrator to SelfTarget; when there are changes, commit is created; no push in test (mock or disable push). Add edge-case test: no changes to copy (assert no commit or no error per current script). References ARC-004.

TASK-005: Add planning launcher initiative-status tests. Create minimal fixture docs/initiatives/<slug>/ with initiative-status.md (and any minimal files launcher reads). Invoke launcher or code path that writes initiative-status; assert output contains expected phase, next action, and execution state section. References ARC-005.

TASK-006: (Optional) Add initiative_resume tests. Create fixture initiative-status.md files (e.g. branched, merge-pending, tasks-complete). Assert printed next action matches expected guidance for each fixture. References ARC-006.

TASK-007: Run initiative tests in CI and document. Ensure CI runs the initiative test suite (e.g. pytest tests/initiative/). Add to README or project-index how to run initiative tests. References ARC-007.
