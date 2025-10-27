# Tasks: Automated Avatar Pipeline

**Input**: Design documents from `/specs/1-avatar-pipeline/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md (if available)

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2)
- Include exact file paths in descriptions

---

## Phase 1: Setup (Shared Infrastructure)

Purpose: Project initialization and basic structure for prototype spikes

- [ ] T001 [P] [Setup] Create `prototypes/test-inputs/` and add canonical test photo(s) and one short test video.
- [ ] T002 [P] [Setup] Initialize `prototypes/` directories: `p1-base-mesh/`, `p2-nfr/`, `shared/` (create scripts/, demo/, artifacts/ subfolders).
- [ ] T003 [P] [Setup] Add CI-friendly CLI entrypoints: `prototypes/cli/run-p1.ps1` and `prototypes/cli/run-p2.ps1` (scripts accept input paths and output artifact path).
- [ ] T004 [P] [Setup] Add simple demo loader skeleton (`prototypes/shared/demo/index.html`) that can load a glTF and trigger the conversation API.
- [ ] T005 [P] [Setup] Create metric/check scripts: `prototypes/shared/utils/check_size.sh` and `check_viseme_smoke.js` (headless browser smoke test harness).

---

## Phase 2: Priority 1 - Base Mesh & Texture Projection (P1) 🎯 MVP spike

Goal: Produce a reproducible prototype that shrink-wraps/retargets a canonical base mesh,
projects textures, and exports an optimized glTF artifact.

- [ ] T101 [P1] [P1] P1-1: Evaluate and select candidate base meshes (store candidates in `prototypes/p1-base-mesh/assets/`).
- [ ] T102 [P1] [P1] P1-2: Implement alignment and shrink-wrap script (Blender Python). Commit example script at `prototypes/p1-base-mesh/scripts/shrink_wrap.py`.
- [ ] T103 [P1] [P1] P1-3: Implement texture projection and atlas packing script (`prototypes/p1-base-mesh/scripts/project_textures.py`).
- [ ] T104 [P1] [P1] P1-4: Export, optimize, and pack glTF (`prototypes/p1-base-mesh/scripts/export_optimize.sh`) using gltfpack/gltf-pipeline.
- [ ] T105 [P1] [P1] P1-5: Integrate with demo loader and create `prototypes/p1-base-mesh/demo/index.html` showing viseme triggers.
- [ ] T106 [P1] [P1] P1-6: Run automated checks: size, viseme smoke test; store results in `prototypes/p1-base-mesh/artifacts/metrics.json`.
- [ ] T107 [P1] [P1] P1-7: Write spike report `specs/1-avatar-pipeline/research-p1.md` summarizing results, artifacts, and blockers.

---

## Phase 3: Priority 2 - Neural Face Rigging (P2) 🔬 spike

Goal: Evaluate open-source neural face rigging models for automation quality and feasibility.

- [ ] T201 [P2] [P2] P2-1: Survey and shortlist 2–3 NFR model candidates; document repo links and licenses in `specs/1-avatar-pipeline/research-p2.md`.
- [ ] T202 [P2] [P2] P2-2: Create wrappers to run each model locally; place wrappers in `prototypes/p2-nfr/models/`.
- [ ] T203 [P2] [P2] P2-3: Implement conversion scripts to map model outputs to glTF (scripts in `prototypes/p2-nfr/scripts/`).
- [ ] T204 [P2] [P2] P2-4: Produce demo artifacts and integrate with shared demo loader.
- [ ] T205 [P2] [P2] P2-5: Run performance & compute metrics (inference time, VRAM usage) and record results.
- [ ] T206 [P2] [P2] P2-6: Write spike report `specs/1-avatar-pipeline/research-p2.md` with recommendations.

---

## Phase 4: Cross-Cutting & Polish

- [ ] T301 [Cross] [Quality] Add artifact provenance recording to packaging scripts (include input hashes and transformation steps).
- [ ] T302 [Cross] [Automation] Add unit tests and basic CI workflow to run size and viseme smoke tests on produced artifacts.
- [ ] T303 [Cross] [Doc] Create `specs/1-avatar-pipeline/quickstart.md` with instructions to run P1 and P2 prototypes locally.
- [ ] T304 [Cross] [Security] Verify licensing of base meshes and NFR models; document compliance decisions in research notes.

---

## Phase 5: Decision & Next Steps

- [ ] T401 [Decision] [Project] Consolidate P1 and P2 findings and recommend which approach to move into Phase 1 implementation.
- [ ] T402 [Decision] [Project] If hybrid approach chosen, create Phase 1 implementation plan with tasks and milestones.

---

## Notes

- Tasks marked `[P]` may be executed in parallel where no file or artifact conflicts exist.
- Each task should reference exact file paths when implemented.
- Tests are OPTIONAL but highly recommended for P1/P2 deliverables.

