# Architecture — Dry Run Mode (Orchestrator)

This document describes the architecture for adding a Dry Run execution mode to the Orchestrator. In Dry Run, the full enforcement pipeline runs without modifying the target repository; run report and artifacts are written only to the Orchestrator repo (e.g. logs/run-report-dry-run.json). Pipeline order and artifact schemas are unchanged; Dry Run is additive per NFR-3.

ARC-001: CLI and dry-run mode flag. Add --dry-run to orchestrator.py argument parsing; store dry_run: bool in runtime config; pass mode to all pipeline stages and run-report generation. Implements REQ-001, REQ-002.

ARC-002: Run report and artifact location in Dry Run. When --dry-run is set, write run report to <orchestrator_repo>/logs/run-report-dry-run.json (not target repo); include "dry_run": true in report content; run report schema otherwise unchanged. Implements REQ-006, NFR-1, NFR-2.

ARC-003: Write gate for target repository. When Dry Run is set, no code path may write to the target repository; run report and any other outputs go only to the designated artifact location (orchestrator repo logs/). Identify and gate or redirect all write paths (e.g. run report, baseline capture). Implements REQ-004, REQ-008.

ARC-004: Verification steps skipped in Dry Run. When Dry Run is set, skip verification steps (e.g. pytest); record in run report (e.g. verification_steps_skipped: true, reason: "dry-run") so the run is unambiguous. Implements REQ-003 (pipeline runs; verification skipped per Decisions).

ARC-005: No git or remote from runtime in Dry Run. Orchestrator runtime must not initiate git or remote operations when Dry Run is set. Coordinator obligation (no git/publish when Orchestrator was run with --dry-run) is documented in workflow. Implements REQ-005.

ARC-006: Supervisor outputs to artifact location in Dry Run. When Dry Run is set, any supervisor (Reviewer A/B) outputs are written only to the designated artifact location (e.g. Orchestrator logs/). If runtime invokes supervisors, ensure output path is artifact location; otherwise document in workflow. Implements REQ-007.

ARC-007: Dry Run documentation. Document Dry Run in docs/workflow.md (when to use it; run report location; Coordinator must not run git, Cursor edits to target, or publish when --dry-run was used), docs/orchestrator-roadmap.md (--dry-run and artifact location), and orchestrator.py --help. Implements REQ-002 (documentation), REQ-009 (requirement ID format at line start for drift detection), NFR-2.
