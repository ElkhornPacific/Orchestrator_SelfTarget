# Architecture — Executable Enforcement (Orchestrator)

This document describes the architecture for adding executable enforcement scripts so the Coordinator can run deterministic checks (schema, credential scan, catastrophic-failure alert) and fail the cycle on violation. Orchestrator docs and enforcement pipeline remain authoritative; scripts are additive per REQ-002.

ARC-001: Supervisor output schema validator. Python script in scripts/ that accepts Reviewer A and Reviewer B outputs and validates required fields and verdict values per schema; exit 0 (PASS) or 1 (FAIL) with deterministic output. Implements REQ-001, REQ-003.

ARC-002: Credential/secret scanner. Python script in scripts/ that scans staged or modified files for configured credential patterns; exit 0 if none found, 1 if found; deterministic match list; run before commit. Implements REQ-001, REQ-003.

ARC-003: Catastrophic-failure alert generator. Python script in scripts/ that writes logs/alert_catastrophic_*.txt or logs/catastrophic_failure.json with timestamp, affected repos, reason, last known good ref(s). Implements REQ-001, REQ-003.

ARC-004: Workflow documentation for executable enforcement. Single "Executable enforcement scripts" subsection (or integration into existing steps) that documents when and how to run each script; no change to enforcement pipeline order or run-report/GuardContract schemas. Implements REQ-002, REQ-003.

ARC-005: (Optional) Executable enforcement verifier. Python script in scripts/ that verifies all executable enforcement scripts exist and are documented (e.g. check_executable_enforcement.py). Implements REQ-003.
