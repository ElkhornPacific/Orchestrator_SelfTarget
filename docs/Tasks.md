# Tasks — Target Identity Lock (Orchestrator)

TASK-001: Implement target identity capture and path normalization. Add compute_target_identity(target_repo) returning path (Path.resolve()), git_root (git rev-parse --show-toplevel then resolve); add path_under_identity(dest, identity) using Path(dest).resolve().relative_to(identity.path) with try/except. Call compute_target_identity at start of main() after --repo is resolved; pass identity through the pipeline. Store path_posix and git_root_posix for run report. References ARC-001.

TASK-002: Extend run report schema with target_identity and target_identity_verification. Add failure type target_identity_mismatch to failure policy constants or config. When a violation occurs, set failure_type to target_identity_mismatch and populate verification block. References ARC-002.

TASK-003: Add identity gates before every write to the target repo. Before writing run report, baseline capture, or step artifacts to the target, call path_under_identity; on mismatch set overall_status FAIL, record violation, and write run report to orchestrator_root/logs/run-report-identity-fail.json (or equivalent). Do not write to the target repo on mismatch. Ensure dry-run path redirection is unchanged. References ARC-003.

TASK-004: Update docs/workflow.md with Coordinator obligation: before commit/publish in the target repo, re-validate target identity and cwd; on failure do not commit or push. Document including target identity and allowed write paths in Cursor instructions; state that Cursor echo is optional and pre-instruction checks are primary. References ARC-004.

TASK-005: Ensure run report and evidence bundle always include target identity and verification summary. References ARC-005.

TASK-006: Add unit tests for path normalization and path_under_identity; add at least one integration test that simulates identity mismatch and asserts FAIL and run report written to Orchestrator logs with target_identity_mismatch. References ARC-001, ARC-003.
