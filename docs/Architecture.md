# Architecture
# Self-Hardening Mode v1

This architecture implements REQ-001, REQ-002, REQ-003, REQ-004, REQ-005, REQ-006, REQ-007, REQ-008, REQ-009, REQ-010, REQ-011, REQ-012.

## Architecture Decisions (ARC)

ARC-001: The system MUST maintain three separate repositories:
- Orchestrator (product repo)
- Orchestrator_TestTarget (regression fixture)
- Orchestrator_SelfTarget (self-host harness)

ARC-002: The system MUST use a dual supervisor model:
- Supervisor A proposes a change packet
- Supervisor B audits the proposal and must approve
No commit occurs unless both approve.

ARC-003: The system MUST produce a deterministic evidence bundle every cycle, containing:
- pytest output
- orchestrator execution output
- run-report.json
- git diffs for Orchestrator, SelfTarget, and TestTarget as applicable

ARC-004: The executor MUST enforce an allowlist-based edit policy as a hard block. Any write outside allowlist MUST FAIL immediately.

ARC-005: The system MUST use a two-phase promotion workflow:
- stage changes
- run full deterministic enforcement gates
- promote by committing to Orchestrator main only on PASS and dual approval

ARC-006: The system MUST implement catastrophic failure handling:
- reset to last known good commit
- halt automation
- require human intervention after rollback

ARC-007: The system MUST treat enforcement criteria as immutable policy:
- drift must not be bypassed
- tests must not be modified to accept failures
- schema and path policy must not be relaxed to force PASS

## Component Roles

- Executor: Runs commands, applies edits, produces evidence bundle.
- Orchestrator: Deterministic judge that produces PASS or FAIL only.
- Supervisor A: Proposes changes using evidence bundle.
- Supervisor B: Audits and approves or rejects Supervisor A proposal.
- Git: Source of truth for promotion and rollback.

## Data and Authority

- Source code system of record: GitHub repos.
- Target contract system of record: Each target repo’s docs/GuardContract.json.
- Evidence bundle: derived artifacts stored on disk, non-authoritative.

## Operational Guarantees

- No silent skips.
- No partial passes.
- Deterministic sorted violations.
- Absolute path invocation required.
- Drift and enforcement failures halt the cycle without relaxing criteria.