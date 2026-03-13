# PRD: Docker for Orchestrator projects

*Generated for the initiative in docs/initiatives/docker/. Output: docs/initiatives/docker/PRD.md.*

## 1. Executive Summary

A previous planning session and analysis suggested that Docker would aid in hardening the Orchestrator workflow (reproducible environments, isolation, consistent verification). Docker has been installed locally but is not yet used. This initiative researches whether Docker should be used as a container for Orchestrator projects—Orchestrator itself, SelfTarget, TestTarget, or CI/verification runs—and, if appropriate, introduces Docker so that hardening benefits are realized without unnecessary complexity or tool volatility.

## 2. Problem Statement

- **Unused tool.** Docker was adopted on the basis of a hardening recommendation but has not been integrated into the workflow, so the expected benefits (reproducibility, isolation, consistent verification) are not realized.
- **Unclear scope.** It is not yet defined whether Docker should wrap the Orchestrator runtime, target repos (SelfTarget/TestTarget), verification steps (e.g. pytest, orchestrator.py), or CI only—or whether adoption is justified at all.
- **Risk of drift.** Without a clear decision and minimal integration, Docker may remain unused or be introduced in an ad hoc way, increasing maintenance cost or tool dependency without clear benefit.

## 3. Target Users

- **Coordinators** and operators who run the Orchestrator workflow and would benefit from reproducible, isolated environments for verification.
- **Orchestrator maintainers** who want to harden the workflow (consistent environments, reduced "works on my machine" issues) and need a clear recommendation on Docker.
- **CI or future automation** that may run Orchestrator or verification in containers.

## 4. Use Cases

- **UC-1:** An operator or CI runs Orchestrator (or verification steps) inside a container so that the environment is reproducible and isolated from the host.
- **UC-2:** A maintainer evaluates Docker (pros, cons, scope options) and produces a documented recommendation (adopt / pilot / defer) so that the project can decide.
- **UC-3:** If Docker is adopted, verification gates (e.g. pytest, orchestrator.py) can run in a container so that PASS/FAIL is consistent across machines and CI.

## 5. User Stories and Acceptance Criteria

- **US-1:** As a maintainer, I want a clear evaluation of whether Docker should be used for Orchestrator projects so that we either adopt it with defined scope or document why we defer.
- **US-2:** As a Coordinator, I want verification to run in a reproducible environment (if Docker is adopted) so that results are consistent locally and in CI.
- **US-3:** As an operator, I want documentation that states how to run Orchestrator or verification with Docker (if adopted) so that I can follow a single, supported path.

## 6. Functional Requirements

REQ-001: The initiative SHALL produce an evaluation that assesses whether Docker should be used for Orchestrator projects (Orchestrator, SelfTarget, TestTarget, or CI/verification), including pros, cons, and recommended scope or deferral.
REQ-002: The evaluation SHALL be documented in the initiative folder or in docs/ so that the decision and rationale are auditable and reusable.
REQ-003: If the recommendation is to adopt or pilot Docker, the initiative SHALL define the scope (e.g. verification-only, full Orchestrator run, CI only) and SHALL produce or reference artifacts (e.g. Dockerfile, usage docs, or run instructions) sufficient to run the in-scope flow in a container.
REQ-004: If the recommendation is to defer Docker, the initiative SHALL document the rationale and any conditions under which to revisit (e.g. CI requirements, multi-OS support).
REQ-005: The initiative SHALL NOT make Docker mandatory for local development unless explicitly agreed; adoption SHALL allow optional or CI-only use so that contributors without Docker can still run the workflow where possible.

## 7. Non-Functional Requirements

- Evaluation and any integration SHALL consider tool volatility (Docker API, host dependencies, orchestration complexity) and document exit cost (low/medium/high) if we adopt then later remove Docker.
- Any Docker usage SHALL align with the existing workflow (e.g. deterministic gates, PASS/FAIL, evidence bundle) and SHALL NOT weaken enforcement or drift detection.
- Changes SHALL be backwards compatible: existing "run without Docker" paths SHALL remain valid unless the initiative explicitly replaces them with a container-based path and documents the change.

## 8. Success Metrics / KPIs

- A clear, documented recommendation (adopt / pilot / defer) with rationale.
- If adopted: at least one supported path to run Orchestrator or verification in a container, with docs and (if in scope) a Dockerfile or equivalent.
- No regression in workflow determinism or gate semantics; Docker is an optional or additive layer unless otherwise decided.

## 9. UX Considerations

- Documentation SHALL make it obvious whether Docker is required, optional, or CI-only so that operators know what they need to run.
- If Docker is adopted, run instructions SHALL be minimal (e.g. one or two commands) and SHALL live in a discoverable place (e.g. README, docs/workflow.md, or initiative folder).

## 10. Open Questions

- **Scope:** Should Docker wrap the Orchestrator process only, verification steps (pytest, orchestrator.py), target repos (SelfTarget/TestTarget), or CI only? The implementation plan should propose a scope and justify it.
- **Pilot vs full adoption:** Is a time-boxed pilot (e.g. CI-only) preferred before committing to local development use?
- **Baseline:** Should container images be versioned or pinned so that verification remains reproducible over time?

## 11. Assumptions and Dependencies

- Docker is already installed locally; the initiative does not require installing Docker as a deliverable.
- The Orchestrator repo, SelfTarget, and TestTarget are the primary candidates for containerization or container-hosted verification; other targets may be out of scope for this initiative.
- The existing workflow (workflow.md, verification steps, GuardContract, run-report) remains the source of truth for gate semantics; Docker is an execution environment, not a replacement for that logic.

## 12. Risks and Mitigation Plan

- **Risk:** Docker adds complexity (images, networking, host bindings) and can break or slow local iteration.  
  **Mitigation:** Keep Docker optional for local use unless agreed otherwise; prefer CI-only or pilot scope first. Document a simple "run without Docker" path.

- **Risk:** Tool volatility—Docker Desktop, Docker API, or host OS changes could break the integration.  
  **Mitigation:** Document exit cost and pin or version images where possible; avoid deep coupling to Docker-specific features.

- **Risk:** Scope creep—containerizing everything (Orchestrator + all targets) may be unnecessary.  
  **Mitigation:** Evaluation and implementation plan SHALL define minimal scope (e.g. verification only or CI only) and expand only if justified.

## 13. Tool Role and Architecture Risk Assessment

**Tool classification:** Docker is **Processing / Compute** (runtime environment). It is not an Authoring Surface, System of Record, or Ingestion Edge for Orchestrator artifacts; PRD, Architecture, Tasks, GuardContract, and run-report remain in the repo and are not stored inside Docker.

**System of record:** Unchanged. Orchestrator repo (and target repos) remain the system of record for docs and contracts. Docker containers run code and verification; they do not become the SOR for planning or enforcement artifacts.

**High-risk tools:** Docker itself is a dependency; if we adopt it, image provenance, updates, and host requirements become part of the support surface. The evaluation SHALL address this (versioning, exit cost).

**Disallowed as SOR:** Docker or any container registry SHALL NOT be the system of record for PRD, Architecture, Tasks, GuardContract, or workflow-state; those remain in the Orchestrator and target repos.
