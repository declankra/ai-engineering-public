## AUTORESEARCH README

```markdown
# Autoresearch: [Slice Name]

Pre-loop harness for bounded, scored experiments against the [slice name] benchmark.

This scaffold follows the production loop shape: `program.md` is human-owned, `runner` executes measured runs, `score` turns benchmark output into keep/discard/error decisions, and `results.tsv` is append-only.

## What this optimizes

`[primary_metric]` — [definition]

Current target: `>= [target]`

## How it works

1. Read the program file (`program.md`) for objective, constraints, and surfaces
2. Read required context to understand architecture and design rationale
3. Read gold labels to understand what correct output looks like
4. Read latest mismatch artifacts to understand current failure patterns
5. Form hypothesis → make bounded change → run benchmark → evaluate → keep or discard
6. Log every run to `results.tsv` (append-only)

## Key files

| File | Purpose |
|------|---------|
| `program.md` | Human-owned objective, metrics, surfaces, principles |
| `runner.[ts/py]` | Executes one measured experiment and appends to the ledger |
| `score.[ts/py]` | Computes keep/discard/error recommendations from benchmark output and `program.md` |
| `results.tsv` | Append-only experiment ledger |
| `artifacts/` | Per-run output (metrics, mismatches, metadata) |
| `baselines/` | Baseline snapshots for comparison |
| `candidates/` | Candidate changes under evaluation (optional) |

## Standard workflow

```
Edit bounded surface
  → run measured autoresearch command with hypothesis + changed files
  → inspect artifacts (start with mismatches.md)
  → keep or discard based on scorecard
  → if keep: runner records results.tsv and the program baseline is updated intentionally
  → if discard: revert, try different approach
```

## Workflow contract

- Establish a baseline row before iterative experiments: `pnpm autoresearch:[slice] --baseline`.
- After bootstrap, every measured experiment goes through `pnpm autoresearch:[slice] --hypothesis "..." --file ...`.
- Raw benchmark commands are allowed for off-loop diagnostics only when explicitly requested.
- Hard benchmark failures are still loop outcomes. Record them with `decision=error`, metadata, artifacts, and notes so failure modes remain visible.
- Guardrail failures are discarded even if the primary metric improves.

## Current bottleneck

[What the main source of errors is right now — update this after each significant run]

## Running

```
/autoresearch [path-to-this-directory]/program.md
```
```

---
