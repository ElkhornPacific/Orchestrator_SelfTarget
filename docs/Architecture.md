# Architecture: One initiative at a time

This document defines the architecture elements for the one-initiative-at-a-time initiative. Each ARC implements one or more requirements from the PRD; tasks reference these ARCs for drift coverage.

ARC-001: Policy documentation in initiative README and RESUME. Implements REQ-001, REQ-002, REQ-003. docs/initiatives/README.md and docs/initiatives/RESUME.md SHALL each contain the primary statement that only one initiative should be active at a time, that docs/workflow-state.md and target_repo always refer to the current initiative, and that running multiple initiatives in parallel or switching context without completing or abandoning the current one is discouraged. Wording SHALL be identical or near-identical in both files to avoid drift; wording SHALL use "should" and "complete or abandon" so that legitimate context switch remains valid.
