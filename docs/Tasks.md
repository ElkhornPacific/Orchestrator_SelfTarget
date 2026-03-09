# Tasks
# Self-Hardening Mode v1
# Deterministic Behavioral Task Set

TASK-001: Establish self-target repo artifact set
TASK-002: Enforce dual supervisor approval before commit
TASK-003: Produce deterministic evidence bundle each cycle
TASK-004: Enforce allowlist-based edit policy
TASK-005: Enforce two-phase promotion and PASS-gated commit
TASK-006: Prevent enforcement relaxation to force PASS
TASK-007: Halt when drift cannot be corrected
TASK-008: Rollback on catastrophic failure and halt
TASK-009: Enforce strict schemas for supervisor inputs and outputs
TASK-010: Protect secrets and disallow credentials on disk

Each TASK below is a behavioral obligation and MUST reference one or more ARC IDs from Architecture.md.

## TASK-001: Establish self-target repo artifact set
Refs: ARC-001, ARC-003
Acceptance Criteria:
- docs/PRD.md, docs/Architecture.md, docs/Tasks.md exist in Orchestrator_SelfTarget.
- Each artifact contains canonical IDs (REQ / ARC / TASK).
- Drift detection can parse IDs deterministically.

## TASK-002: Enforce dual supervisor approval before commit
Refs: ARC-002, ARC-005
Acceptance Criteria:
- A proposed change packet exists (Supervisor A output).
- An audit decision exists (Supervisor B output).
- No commit can occur without Supervisor B APPROVE and Supervisor A approval.

## TASK-003: Produce deterministic evidence bundle each cycle
Refs: ARC-003
Acceptance Criteria:
- Each cycle produces:
  - pytest output
  - orchestrator execution output
  - run-report.json
  - git diffs
- Evidence bundle filenames and ordering are deterministic for identical inputs.

## TASK-004: Enforce allowlist-based edit policy
Refs: ARC-004
Acceptance Criteria:
- Any attempted write outside allowlist FAILs immediately.
- Allowed edits are limited to explicitly approved paths.

## TASK-005: Enforce two-phase promotion and PASS-gated commit
Refs: ARC-005
Acceptance Criteria:
- Changes are staged prior to promotion.
- Full enforcement gates run against SelfTarget and TestTarget.
- Commit to Orchestrator main occurs only when:
  - all gates PASS
  - Supervisor A approves
  - Supervisor B approves

## TASK-006: Prevent enforcement relaxation to force PASS
Refs: ARC-007
Acceptance Criteria:
- Proposals that modify tests or criteria to accept failures are rejected.
- Proposals that reduce drift coverage or weaken schema/path policy are rejected.
- If a PASS cannot be achieved without relaxation, the cycle halts.

## TASK-007: Halt when drift cannot be corrected
Refs: ARC-007
Acceptance Criteria:
- Drift failures produce FAIL and halt the cycle.
- If drift is not resolvable within configured limits, automation halts and requires human intervention.

## TASK-008: Rollback on catastrophic failure and halt
Refs: ARC-006
Acceptance Criteria:
- Catastrophic failures trigger reset to last known good commit.
- Automation halts after rollback.
- An alert artifact is produced for operator review.

## TASK-009: Enforce strict schemas for supervisor inputs and outputs
Refs: ARC-002, ARC-003
Acceptance Criteria:
- Supervisor A outputs conform to a strict schema.
- Supervisor B outputs conform to a strict schema.
- Invalid schemas FAIL the cycle deterministically.

## TASK-010: Protect secrets and disallow credentials on disk
Refs: ARC-002
Acceptance Criteria:
- OpenAI API credentials are sourced from environment or secrets storage.
- No credentials are written to disk or committed.