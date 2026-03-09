# Tasks — Executable Enforcement (Orchestrator)

TASK-001: Implement supervisor output schema validator script (scripts/check_supervisor_output_schema.py). Validates Reviewer A/B output per schema; exit 0/1; deterministic. References ARC-001.

TASK-002: Implement credential/secret scanner script (scripts/scan_credentials.py). Scan staged or modified files for configured patterns; exit 0/1; deterministic match output; run before commit. References ARC-002.

TASK-003: Implement catastrophic-failure alert generator script (scripts/write_catastrophic_alert.py). Writes logs/alert_catastrophic_*.txt or logs/catastrophic_failure.json with timestamp, affected repos, reason, last known good ref(s). References ARC-003.

TASK-004: Document executable enforcement scripts in workflow. Add "Executable enforcement scripts" subsection (or integrate into steps) so Coordinator knows when and how to run each script; wire schema validator, credential scanner, and alert generator to the relevant MUST steps. References ARC-004.

TASK-005: (Optional) Implement check_executable_enforcement.py to verify all executable enforcement scripts are present and documented. References ARC-005.
