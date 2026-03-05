# PRD
# Self-Hardening Mode v1
# Lights Out Workflow – Orchestrator Enforcement Engine

## Executive Summary

Self-Hardening Mode v1 enables Orchestrator to harden itself autonomously using a dual supervisor model built on the ChatGPT API, while preserving deterministic structural enforcement and preventing drift from specifications.

This mode must operate without manual iteration during normal cycles. It must halt safely when structural drift or enforcement failure cannot be corrected without weakening the enforcement criteria.

## Problem Statement

Manual iteration between the executor environment and the reviewer consumes too much time and blocks the goal of background development. The workflow must produce enterprise-grade software without drift from specifications, and must not permit silent bypass of structural rules.

## Target User

Solo operator.

## Use Cases

1. Orchestrator autonomously improves its enforcement engine implementation while remaining within strict behavioral constraints.
2. Orchestrator validates itself against:
   - Orchestrator_SelfTarget (self-host harness)
   - Orchestrator_TestTarget (regression fixture)
3. On failure, Orchestrator halts and does not continue development if the failure cannot be resolved without relaxing enforcement.

## User Stories and Acceptance Criteria

### Story: Autonomous iteration with strict enforcement
As the operator, I want Orchestrator to iterate autonomously so development proceeds in the background without consuming my time.

Acceptance criteria:
- A cycle executes deterministically and produces PASS or FAIL only.
- No commits are made unless all enforcement gates PASS across both SelfTarget and TestTarget.
- If any gate FAILs and cannot be corrected within configured limits, the system halts.

### Story: Drift must not pass
As the operator, I want drift results to be detected and never allowed to pass.

Acceptance criteria:
- Drift detection violations always produce FAIL.
- The system must not modify tests or pass/fail criteria in order to force PASS.

### Story: Safe rollback on catastrophic failure
As the operator, I want the system to recover safely if a catastrophic failure occurs.

Acceptance criteria:
- Catastrophic failures trigger reset to last known good commit.
- Automation halts and requires human intervention after rollback.

## Functional Requirements (REQ)

REQ-001: The system MUST require a dual-supervisor approval model where Supervisor A proposes changes and Supervisor B audits and approves before commit.

REQ-002: The system MUST run Orchestrator deterministically against both Orchestrator_SelfTarget and Orchestrator_TestTarget in each cycle.

REQ-003: The system MUST produce a deterministic evidence bundle for every cycle, including:
- pytest output
- orchestrator execution output
- run-report.json
- git diffs for each repo involved

REQ-004: The system MUST enforce an allowlist-based edit policy. Any attempted edit outside the allowlist MUST FAIL immediately.

REQ-005: The system MUST apply a two-phase promotion workflow. Changes MUST be staged and verified before promotion.

REQ-006: The system MUST auto-commit directly to Orchestrator main ONLY after:
- all enforcement gates PASS
- Supervisor A approves
- Supervisor B approves

REQ-007: The system MUST NOT relax or weaken enforcement criteria to make PASS, including:
- modifying tests to accept failures
- reducing drift coverage requirements
- weakening schema or path policy constraints

REQ-008: The system MUST halt when drift or enforcement failures cannot be corrected without violating REQ-007.

REQ-009: The system MUST require a clean working tree to start a cycle.

REQ-010: On catastrophic failure, the system MUST reset to last known good commit, then halt and require human intervention.

REQ-011: Supervisor inputs and outputs MUST conform to strict schemas. Invalid supervisor outputs MUST FAIL the cycle.

REQ-012: OpenAI API credentials MUST NOT be written to disk or committed to any repo.

## Non-Functional Requirements

- Deterministic behavior for identical inputs.
- No silent skipping of enforcement stages.
- No partial PASS states.
- Deterministic ordering of violations.
- Reproducible from a clean clone.

## Success Metrics

- Drift violations are never allowed to pass.
- Autonomous cycles complete without human intervention during normal operation.
- Regression targets consistently PASS when no intentional violations exist.
- Automatic halt occurs when failures cannot be resolved within constraints.

## Non-Goals

- No self-modifying schema changes without explicit human intervention.
- No automatic rewriting of PRD, Architecture, or Tasks to match implementation.
- No automatic relaxation of enforcement thresholds.
- No unbounded refactoring of Orchestrator outside the allowed edit surface.

## Open Questions

- Maximum iterations per automated run.
- Size threshold for diffs before halting.
- Exact categorization of catastrophic failures for rollback.

## Tool Role and Authority

- GitHub repos are the authoritative system of record for code.
- Each target repo’s docs/GuardContract.json is authoritative for that target.
- The ChatGPT API supervisors are decision-makers but not systems of record.
- Logs and run-report.json are evidence artifacts, not systems of record.