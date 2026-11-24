# Agent instructions for VIctory shader work

## Mission
Work only on the Xbox shader translation and validation path, primarily `crates/victory-shader`, and directly related GPU pipeline interfaces when a demonstrated shader blocker requires it. The goal is correctness, diagnosability, and measurable coverage—not superficial increases in translated-shader counts.

## Read first
- `README.md` for current status, requirements, diagnostics, and safety warnings.
- `docs/architecture.md`, especially the "Shaders" section.
- `crates/victory-shader/Cargo.toml` and relevant source/tests before proposing edits.

## Non-negotiable rules
1. Never claim a shader is fixed without a reproducible test or validation evidence.
2. Do not silently replace unsupported semantics with zero, no-op, or approximate behavior merely to make translation succeed.
3. Preserve lane, EXEC-mask, wave, memory-ordering, atomic, descriptor, and resource semantics exactly. If correctness cannot be established, report the limitation.
4. Do not remove failing tests, weaken assertions, or alter unrelated subsystems to get a green build.
5. Never add, request, download, or commit proprietary game files, title binaries, extracted assets, shader dumps, credentials, or copyrighted user data. Use synthetic/minimal fixtures only.
6. Do not run the game on a real GPU as part of automated work. The README documents a current GPU-hang/driver-reset risk. Use safe unit tests and explicitly authorized diagnostic workflows only.
7. Keep changes small and isolated. One issue per branch/commit where practical. Do not merge or push directly to `main`.
8. Before editing, inspect current implementation and tests. Before finishing, run the narrowest relevant tests, then `cargo test --workspace` when feasible.
9. Report exact commands, outcomes, untested cases, and remaining uncertainty. Distinguish compile success from semantic correctness and real-GPU validation.

## Required workflow for every task
1. Identify a concrete failing behavior from existing code, tests, issue reports, or user-provided diagnostics.
2. Trace it through the architecture and identify the smallest responsible layer.
3. Write or extend a synthetic regression test that fails before the fix.
4. Implement the smallest semantics-preserving change.
5. Run formatting and relevant tests.
6. Summarize: root cause, changed files, test evidence, risks, and next blocker.

## Focus areas
Prioritize independently testable gaps:
- Wave-size / Wave64 assumptions and wave operations.
- 64-bit image atomics and their exact host capability/semantics.
- Merged geometry-stage behavior.
- Shader translation failures that cause world draws/dispatches to be dropped.
- Diagnostics that make unsupported opcodes/features and shader hashes actionable.

Do not assume these are all solvable in the shader crate. Some require host D3D12 capability checks, pipeline metadata, or GPU features. Prove the boundary before changing code.
