<!--
Sync Impact Report

- Version change: TEMPLATE → 1.0.0
- Modified principles:
	- [PRINCIPLE_1_NAME] → Lightweight Web-First
	- [PRINCIPLE_2_NAME] → No Recurring/Streaming Costs
	- [PRINCIPLE_3_NAME] → Automation-First Pipeline
	- [PRINCIPLE_4_NAME] → Refinable Outputs
	- [PRINCIPLE_5_NAME] → Conversation-Ready Rigging & Runtime
- Added sections:
	- Constraints & Technical Standards
	- Development Workflow & Quality Gates
- Removed sections: none
- Templates requiring updates:
	- .specify/templates/plan-template.md ✅ updated (Constitution Check populated)
	- .specify/templates/spec-template.md ✅ reviewed (no mandatory change required)
	- .specify/templates/tasks-template.md ✅ reviewed (no mandatory change required)
	- .specify/templates/agent-file-template.md ⚠ pending (review recommended for automation guidance)
- Follow-up TODOs: none
-->

# Spec Kit Template (Copilot PS) Constitution

## Core Principles

### Lightweight Web-First
The project MUST prioritize implementations that are performant in modern web browsers. Deliverables
MUST target browser-capable runtimes (e.g., WebGL, WebGPU, or WebAssembly) and expose a clear
performance budget in each plan (to be measured and validated by the project's performance tests).
Rationale: the product's primary surface is the web — this constraint prevents solutions that are
infeasible to ship to end users.

### No Recurring/Streaming Costs
The project MUST NOT rely on recurring paid GPU inference services, pixel-streaming services, or
per-session cloud GPUs for runtime functionality. Cloud compute is permitted for build-time tasks
or offline preprocessing (billed as CI/build costs), but the delivered runtime artifact MUST run
without ongoing per-user cloud GPU dependencies. Rationale: control costs and enable broad
distribution.

### Automation-First Pipeline
All pipeline steps for generating, validating, and packaging the avatar MUST be automatable and
executable in CI/CD. Tasks that require manual intervention MUST be documented and kept to a
minimum with a clear migration path to automation. Rationale: enables scalability, reproducibility,
and predictable releases.

### Refinable Outputs
The pipeline's outputs MUST be refinable: intermediate artifacts, metadata, and transformations
MUST be versioned so teams can iterate and improve quality post‑delivery. V1 artifacts may be
coarse, but the process MUST accept and record refinement steps (e.g., re-rig, re-skin, or re-bake).
Rationale: quality improves through iteration — the project requires traceable refinement paths.

### Conversation-Ready Rigging & Runtime
Final deliverables MUST be a fully-rigged, conversation-ready avatar: exported runtime rigs,
viseme mappings, lip-sync hooks, expression controls, and a minimal API for conversational events
(e.g., onUtteranceStart, onViseme, onExpression). The runtime interface MUST be documented and
testable. Rationale: the avatar must be production-capable for conversational scenarios.

## Constraints & Technical Standards

- Allowed runtime targets: WebGL, WebGPU, WebAssembly, or native web runtimes. If a plan targets
	another runtime it MUST include a justification and a migration plan to a web-capable runtime.
- Allowed cloud usage: Build-time and preprocessing compute in CI is permitted. No runtime
	dependence on paid GPU streaming or per-session GPU inference.
- Preferred asset formats: glTF 2.0 (binary or embedded), optimized textures (compressed), and
	streaming-friendly meshes. Plans MUST document asset size budgets and fallback behavior.
- Accessibility & privacy: conversational data collection MUST follow documented privacy guidance
	(see runtime guidance). Sensitive data MUST be handled per repository policies.

## Development Workflow & Quality Gates

- Every feature plan MUST include a "Constitution Check" section that lists how the plan satisfies
	each Core Principle. The `/speckit.plan` output MUST surface gate violations that require
	explicit justification.
- Performance gate: plans MUST include measurable performance goals and a test that can be
	executed in CI (simple local or headless browser performance smoke test or asset size checks).
- Automation gate: builds that produce runtime artifacts MUST be reproducible in CI and have a
	documented CLI or script entrypoint for packaging the web deliverable.
- Refinement gate: artifacts MUST be versioned and the plan MUST include instructions to re-run
	refinement steps with deterministic inputs.

## Governance

Amendments to this constitution MUST be proposed as a repository pull request against
`.specify/memory/constitution.md`. Each amendment PR MUST include:

1. A clear description of the change and the rationale.
2. A migration or compliance plan for any existing artifacts or plans affected.
3. At least one maintainer approval and a successful CI run that validates the "Constitution
	 Check" for a changed plan (where applicable).

Versioning policy (semantic):

- MAJOR: Backwards-incompatible removals or redefinitions of core principles or governance.
- MINOR: Addition of a new principle or material expansion of guidance.
- PATCH: Wording clarifications, typos, or non-semantic refinements.

Compliance review expectations:

- Every `/speckit.plan` invocation MUST include a Constitution Check summary. Plans that violate
	any non-negotiable principle MUST be accompanied by an explicit, documented exception and a
	migration plan; exceptions MUST be time-limited and approved as part of the PR.

**Version**: 1.0.0 | **Ratified**: 2025-10-27 | **Last Amended**: 2025-10-27

