# VIctory Shader Lab

This directory coordinates shader-only investigation. It does not change runtime behavior by itself.

## Current baseline (from repository README, 2026-11-23)
- Menus: all listed shaders translated.
- World mesh: 8,191 / 8,506.
- World vertex/pixel: 10,617 / 12,346.
- World compute: 1,643 / 2,539.
- Known gaps include 64-bit image atomics, merged geometry stages, ray-tracing builder shaders, and compute programs requiring 64-lane waves.
- The world currently renders incompletely and may hang/reset a real GPU. Automated work must not launch the title on a real GPU.

These are repository-reported counts, not independently reproduced by this lab yet.

## First objective
Build a reproducible inventory of shader translation failures, group failures by root cause/opcode/feature, and rank fixes by how many shaders and pipeline types they unblock. Do not begin by making speculative code changes.

## Triage record
For every failure, capture:
- shader hash / stable identifier (do not include proprietary shader bytecode in public reports)
- stage and shader model, if known
- source instruction/opcode or unsupported feature
- translation phase and exact diagnostic
- whether failure is parse, IR construction, structurization, resource analysis, DXIL emission, validation, or host pipeline creation
- minimal synthetic reproducer and test location
- affected feature/capability and required host D3D12 support
- proposed semantics-preserving implementation
- tests run and results
- confidence and remaining unknowns

## Priority rubric
1. Correctness and safety: never emit semantically incorrect DXIL to improve a count.
2. Reach: number of distinct failures explained by one root cause.
3. Unblock value: whether it restores previously dropped world draws/dispatches.
4. Testability: can a synthetic regression test establish correctness?
5. Cost: complexity and dependency on unavailable GPU hardware/features.

## Work streams
- Inventory: collect and classify current translator diagnostics.
- Semantics: investigate Wave/EXEC and lane-sensitive behavior.
- Atomics: capability mapping and memory semantics.
- Pipeline stages: geometry/mesh/ray-tracing stage interfaces.
- Validation: regression fixtures, workspace tests, and report generation.

## Seven-day target
The seven-day target is a time-boxed engineering sprint, not a guarantee that every blocker can be solved. Deliverables are:
1. Baseline build/test result.
2. Ranked failure inventory with root-cause clusters.
3. Reproducers for the highest-impact clusters.
4. Small, reviewed fixes with regression tests where correctness is provable.
5. Explicit list of hardware-dependent and unresolved gaps.

## Safety
Use synthetic fixtures and public source code only. Do not commit game data, proprietary binaries, shader dumps, tokens, or other secrets. Do not run real-GPU world tests: the project README documents a driver-reset risk.
