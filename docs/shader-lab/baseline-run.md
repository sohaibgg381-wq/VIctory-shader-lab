# Baseline shader translation run

Purpose: collect a reproducible failure inventory before changing translator code.

## Important
- Run this against the user's own local game-data directory. Do not upload or commit game files, archives, shader bytecode, extracted assets, or dumps.
- This pass does **not** launch the game and does not create D3D12 pipelines. It is intended to run without a compatible gaming GPU.
- Do not add `--validate` or `--pipelines` for this first pass. Those enable Windows DXIL validation and host D3D12 pipeline creation, respectively.
- On a low-memory or older CPU, start with `--jobs 2`. This limits worker threads; it does not make the scan instantaneous.

## Command (PowerShell, from the repository root)

First build/run the CLI using the pinned toolchain:

```powershell
cargo run --release -p victory -- translate "D:\PATH\TO\YOUR\GAME\DATA" --list --jobs 2 *> shader-baseline.txt
```

Replace the path with the directory containing the locally available game data. If the data is in multiple directories, pass each directory as a separate positional argument.

If the release build takes too long or fails due to memory pressure, try the default profile:

```powershell
cargo run -p victory -- translate "D:\PATH\TO\YOUR\GAME\DATA" --list --jobs 2 *> shader-baseline.txt
```

## What to send back

Send:
1. The summary lines at the top (containers, programs, structured, resources, I/O, DXIL counts).
2. The final failure-reason summary, sorted by frequency.
3. A few representative per-program failure lines for the most frequent reasons.

Before sharing, replace local absolute paths, Windows usernames, and any private directory names with `<LOCAL_PATH>`. Do not send `.dxil`, `.bin`, `.xsh`, archives, shader dumps, or game files.

## Interpretation

The command reports several distinct gates:
- Native program extraction / IR construction.
- Control-flow structurization.
- Resource tracking.
- Stage I/O raising.
- DXIL emission.

A program counted as translated at the IR stage is not necessarily emitted as DXIL. An emitted shader is not necessarily validator-clean, pipeline-creatable, or correct on real hardware. Keep these measurements separate.

## Next action after the baseline

Cluster failures by exact reason and stage. Select the highest-impact cluster that can be reproduced with a synthetic fixture. Add a failing regression test first, then implement one narrow semantics-preserving fix on a dedicated branch. Do not infer correctness from a higher translated count alone.