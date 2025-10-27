# Implementation Plan: Automated Avatar Pipeline

**Branch**: `1-avatar-pipeline` | **Date**: 2025-10-27 | **Spec**: ../spec.md
**Input**: Feature specification from `/specs/1-avatar-pipeline/spec.md`

## Summary

Objective: define and evaluate several processing approaches to convert user-provided photos
and short video into a single, lightweight, fully-rigged conversation-ready 3D avatar (glTF/glb)
for web delivery. This plan focuses on parallel R&D (technical spikes) for the top two high-priority
approaches (Base Mesh & Texture Projection; Neural Face Rigging) and defines experiments,
metrics, and acceptance for each approach.

## Technical Context

**Target Platform**: Web (WebGL/WebGPU/WASM demo page)
**Primary Dependencies**: Blender (for scripted pipelines), glTF tooling (gltf-pipeline, gltfpack),
open-source neural face rigging libraries (TBD), image-processing (OpenCV), CLI orchestration
(scripts), and CI runners (Linux/Windows).
**Storage**: Temporary build artifacts in CI; stable artifacts stored in an artifact bucket or
repository (TBD in implementation plan).
**Testing**: Headless browser tests (Puppeteer/Playwright), unit tests for tooling scripts,
human perceptual tests for motion/viseme quality.
**Performance Goals**: Default artifact <= 15 MB compressed; demo load <3s on typical consumer
connection; single-photo end-to-end processing spike target: prototype complete within 60m on a
well-provisioned CI agent for dev tests (not final production budget).
**Constraints**: No runtime dependence on per-user cloud GPU or pixel streaming services. Build-
time compute (CI) may use cloud GPUs for offline preprocessing only.
**Scale/Scope**: R&D spikes for 2 approaches in parallel, each capped to 2 weeks of focused
investigation (extendable based on outcomes).

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- Lightweight Web-First: Spike outputs MUST demonstrate a web-compatible runtime package or a
  clear migration path. Spikes will produce a small demo (glTF + simple HTML loader) to exercise
  the conversation API surface and measure artifact size / load time.
- No Recurring/Streaming Costs: All runtime artifacts produced by spikes MUST be runnable in a
  local browser demo without cloud GPU services. Any heavy GPU work is restricted to build-time
  processing and must be clearly marked in experiment notes.
- Automation-First Pipeline: Each spike must produce a scripted CLI entrypoint that can be run
  in CI to reproduce the experiment. Manual steps are allowed during exploration but must be
  documented and targeted for automation in follow-ups.
- Refinable Outputs: Spikes MUST capture provenance: input hashes, transformation steps, and
  artifacts. Experiments will version outputs (simple timestamped/hashed manifests) to validate
  refinement reproducibility.
- Conversation-Ready Rigging & Runtime: Spikes MUST include a minimal runtime API for conversational
  events (onUtteranceStart, onViseme, onExpression) in the demo page and an acceptance test that
  exercises viseme triggering.

If any gate cannot be met during a spike, the spike deliverable MUST include a time-limited
exception and a migration path to meet the gate in a subsequent milestone.

## Processing Approaches (R&D spikes)

Overview: run parallel technical spikes for Priority 1 and Priority 2. Each spike is scoped to
produce a reproducible artifact, automated scripts to run the experiment, a short research note,
and objective metrics comparing quality, artifact size, automation effort, and runtime viability.

### Priority 1 (High) — Base Mesh & Texture Projection

Hypothesis: The fastest path to a rigged avatar is to use a curated, pre-rigged base mesh and
project the user's textures onto that mesh's UVs after aligning and shrink-wrapping.

R&D deliverables:

- Prototype script that: (1) aligns a canonical base mesh to input geometry, (2) shrink-wraps or
  retargets the base mesh to the input geometry, (3) projects photo textures onto base UVs, (4)
  exports a glTF runtime package with the rig and texture atlases.

- Demo page that loads the resulting glTF and exercises minimal conversation events.

- Automation: CLI script that accepts input photos or video and produces the artifact.

- Research note: licensing of base mesh, tools tested, failure cases, and automation blockers.

Experiment metrics / acceptance:

- Artifact size (compressed glb) — target <= 15 MB for default tier.

- Quality: per-pixel texture fidelity (PSNR/SSIM vs source crops) and perceptual check via a
  small internal ranking (N=5) for visual plausibility.

- Motion suitability: produced rig MUST expose blendshapes/controls needed for visemes; test
  that the demo can map basic viseme timings to animations.

- Automation effort: number of manual steps required (goal: <= 2 manual interventions).

Timebox: 2 weeks spike (parallel to NFR spike). If baseline flow fails early, document blockers
and propose mitigations (different base mesh, improved alignment, additional input requirements).

### Priority 2 (High) — Neural Face Rigging (NFR)

Hypothesis: Neural/regression models can produce a rigged mesh directly from images/video with
minimal manual intervention.

R&D deliverables:

- Identify and test 2–3 candidate open-source NFR model implementations (e.g., repos found in
  research, adapted to run locally or in CI). Document license and reproducibility.

- Pipeline wrapper that converts model outputs to a glTF runtime package with required runtime
  rig info and viseme mapping (where possible).

- Demo page and automated runner that exercises the conversation API and basic viseme timing.

- Research note including model performance, failure modes, compute requirements, and
  automation feasibility.

Experiment metrics / acceptance:

- Quality: mesh accuracy vs ground-truth scans (if available) or relative perceptual ranking vs
  Priority 1 baseline (internal N=5 judges). Measure viseme mapping alignment where available.

- Artifact size and completeness: does the model produce all required runtime artifacts (rig,
  blendshapes) or is post-processing needed?

- Compute & automation: measure inference time on a standard dev GPU and feasibility of
  running in CI (build-time only). Record if model requires GPUs with significant VRAM.

Timebox: 2 weeks spike (parallel). If NFR shows strong promise, escalate to a targeted
implementation plan; if it requires heavy manual post-processing, document automation gaps.

### Priority 3 (Medium) — Scripted Toolkit (Blender automation)

Hypothesis: Semi-manual tools can be scripted to achieve similar outputs with more engineering
effort but higher deterministic control.

R&D deliverables (investigate only if 1 or 2 show non-viability or as a complementary path):

- Prototype Blender Python script that automates face alignment, retopology calls, and UV
  packing using community tools.

- Evaluation of runtime artifact quality and automation feasibility.

Timebox: exploratory (1 week) after initial spikes if needed.

### Priority 4 (Low) — Physics-Based Simulation

Hypothesis: A first-principles physics approach will be robust long-term. Tabled for now;
only considered if other approaches fail.

## Project Structure

### Documentation (this feature)

```text
specs/1-avatar-pipeline/
├── plan.md              # This file
├── research.md          # Spike reports (created at spike end)
├── spec.md              # Feature spec (source of truth)
├── quickstart.md        # How to run prototype/demo locally
├── contracts/           # If API contracts are needed
└── tasks.md             # Generated by /speckit.tasks when ready
```

### Source Code (prototype)

```text
prototypes/
├── p1-base-mesh/
│   ├── scripts/         # shrink-wrap, texture projection scripts
│   ├── demo/            # demo page and loader
│   └── artifacts/       # sample outputs
├── p2-nfr/
│   ├── models/          # model wrappers
│   ├── scripts/         # conversion + glTF packaging
│   └── demo/
└── shared/
    ├── cli/             # orchestrator scripts
    └── utils/
```

**Structure Decision**: Prototypes live under `prototypes/` with per-approach directories to
encapsulate tooling and make it straightforward to retire unsuccessful approaches.

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| GPU Heavy Models (P2) | NFRs may require high-memory inference GPUs | Run on CI build-time only; document limits and provide fallbacks (P1) |
| Licensed Base Mesh | May need high-quality pre-rigged mesh with commercial license | Seek permissive free meshes or generate one via community assets; document licensing |

## Spike Execution Plan & Tasks


### Shared setup (Day 0)

- S001 Prepare canonical test inputs (1 photo set, 1 short video set) and store under `prototypes/test-inputs/`.

- S002 Create reproducible CLI entrypoints: `prototypes/cli/run-p1.ps1` and `run-p2.ps1` that accept input paths and output artifact path.

- S003 Define success metrics scripts (size check, simple viseme smoke test) and make them runnable in CI.


### Priority 1 tasks (P1)

- P1-1 Evaluate candidate base meshes (3 candidates) and select one based on topology, UV layout, and license.

- P1-2 Implement alignment + shrink-wrap script (Blender Python or alternative toolchain).

- P1-3 Implement texture projection pipeline and atlas packing.

- P1-4 Export and optimize glTF (gltfpack/gltf-pipeline) and produce demo artifact.

- P1-5 Run artifact size and viseme smoke tests; collect metrics.

- P1-6 Write spike report: findings, scripts, demo link, next steps.


### Priority 2 tasks (P2)

- P2-1 Survey open-source NFR models; shortlist 2 candidates with runnable code and permissive licenses.

- P2-2 Integrate model(s) to accept photo/video input and produce a mesh + rig output.

- P2-3 Convert model output into glTF format and add minimal runtime glue (viseme mapping where available).

- P2-4 Run quality and compute metrics: inference time, memory usage, artifact completeness.

- P2-5 Write spike report: model performance, automation effort, recommended path.

## Deliverables

- `prototypes/p1-base-mesh/` and `prototypes/p2-nfr/` prototype artifacts and scripts.
- `research.md` spike reports for P1 and P2 (documenting metrics, blockers, and recommended
  next steps).
- Demo page(s) that can load produced glTFs and exercise the conversation API.
- Decision recommendation listing which approach to pursue for Phase 1 implementation.

## Timeline

- Week 0 (Day 0): Setup test inputs and shared scripts
- Week 1-2: Run P1 and P2 spikes in parallel (deliverables: prototype artifacts, demo, research
  notes)
- End of Week 2: Spike reports delivered and a decision meeting held to choose a path forward
  (P1, P2, or hybrid)

## Risks & Mitigations

- Risk: NFR models require heavy GPUs and long inference times (Mitigation: keep P1 as robust
  fallback and limit NFR to offline build-time runs).
- Risk: Base mesh approach produces poor texture/UV matches for diverse faces (Mitigation: favor
  high-quality atlas packing and multi-view blending; add refinement tier).
- Risk: Licenses or model IP restrict production use (Mitigation: document licenses, seek
  permissive alternatives, or budget for licenses in Phase 1).

## How to run locally (Quickstart)

1. Ensure you have Blender (with command-line Python) and Node/Python tooling for glTF optimization.
2. Place test inputs under `prototypes/test-inputs/`.
3. Run `.
un-p1.ps1 --input ./prototypes/test-inputs/set1 --out ./prototypes/p1-base-mesh/artifacts/out.glb` (example)
4. Open `prototypes/p1-base-mesh/demo/index.html` to load the artifact and exercise the simple
   conversation API.

## Notes

- This plan intentionally focuses R&D effort on P1 and P2 as requested. P3 (scripted toolkits)
  remains a fallback and P4 is tabled.
- After spike completion, create `/specs/1-avatar-pipeline/research.md` to summarize results and
  update `plan.md` with chosen implementation detail for Phase 1.
