Executable enforcement (Orchestrator)

REQ-001: Provide executable checks for supervisor output schema (TASK-009), credential/secret scan before commit (TASK-010), and catastrophic-failure alert generation (TASK-008). Each must be invocable by the Coordinator and exit 0 (PASS) or 1 (FAIL) with clear, deterministic output.

REQ-002: Do not change the enforcement pipeline order in docs/enforcement-pipeline.md or the artifact schemas (run-report, GuardContract). New scripts are additive.

REQ-003: Document each new script in the workflow (or in a single "Executable enforcement scripts" subsection) so the Coordinator knows when and how to run it.

Goal: Turn selected workflow policy rules into executable enforcement so the Coordinator (or orchestrator runtime) can run deterministic checks and fail the cycle on violation, instead of relying only on documented "MUST" and manual verification.

Already done:
- Orchestrator runtime (orchestrator.py) enforces schema, path policy, immutable, invariants, drift, verification steps, failure policy per docs/enforcement-pipeline.md.
- Allowlist check: scripts/check_edit_allowlist.py used in Step 2b; workflow mandates running it before Orchestrator.

Remaining work (to be refined in Architecture/Tasks):
- Supervisor output schema validator: script that accepts Reviewer A and Reviewer B outputs and validates required fields and verdict values per TASK-009.
- Credential/secret scanner: script that scans staged or modified files for configured patterns; FAIL if found; run before commit.
- Catastrophic-failure alert generator: script that writes logs/alert_catastrophic_*.txt or logs/catastrophic_failure.json with timestamp, affected repos, reason, last known good ref(s).
- Wire each script into the workflow steps where the Coordinator currently has a "MUST" check.
- Optionally: check_executable_enforcement.py to verify all executable enforcement scripts are present and documented.

Context (policy-only today):
- Supervisor output schema (TASK-009): Coordinator MUST validate proposed change packet and audit decision; no validator script.
- Credential scan (TASK-010): Coordinator MUST ensure no credential patterns in staged/modified files; workflow says "Optionally run a grep or script"; no dedicated script.
- Catastrophic-failure alert (TASK-008): Procedure requires writing an alert artifact; no script to generate it.

Scope:
- In scope: Python scripts in scripts/ for schema validator, credential scanner, alert generator. Each deterministic, invocable by Coordinator, exit 0/1.
- Out of scope: Dry Run and Target Identity Lock (roadmap); changing orchestrator.py gate order; automating enforcement relaxation detection (TASK-006 remains Reviewer obligation).

Constraints:
- Orchestrator docs and enforcement pipeline remain authoritative. Scripts must be deterministic and safe to run in CI or by the Coordinator. Prefer Python in scripts/ for consistency with check_edit_allowlist.py and check_compound_integration.py.

System of Record: Orchestrator repo (D:\GitHub\Orchestrator).
Where will tasks be created: docs/Tasks.md (or a new "Executable enforcement" section in Tasks.md, or docs/executable-enforcement-tasks.md as agreed).
