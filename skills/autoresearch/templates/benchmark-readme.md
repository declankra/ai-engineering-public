## BENCHMARK README

```markdown
# [Slice Name] Benchmark

## What this measures

[1-2 sentences: what AI behavior this benchmark evaluates and why it matters]

## Scoring

- **Primary metric:** `[metric_name]` — [definition]
- **Segmented scoring:** results are broken down by [segments — e.g., review status (accepted/pending/all), source, cohort, category, document type]
- **Per-field metrics:** precision, recall, F1 for each scored field
- **Mismatch artifacts:** every disagreement between expected and actual output is recorded with full context for debugging

### Scored fields

[List every field that gets scored, e.g.:]
- `entityName`, `entityType`, `category`, `status`
- `rawValue`, `normalizedValue`, `unit`, `confidence`
- `sourceReference`, `classification`, `extractedDate`

### Fields NOT scored (context only)

These appear in the gold labels for human review but are not used in metric computation:
- [e.g., `reviewNotes` — why a label was set a certain way]
- [e.g., `sourceValues` — raw values from the source document]
- [e.g., `seedReferences` — cross-references from other data sources]
- [e.g., `aiReasoning` — model's self-reported reasoning (tracked but not scored)]

## Gold labels

Gold labels live in each project folder under `projects/[project-id]/`.

- Labels are reviewed by [who — e.g., "product owner", "domain expert", "stakeholder"]
- Each label has a `reviewStatus`: `accepted` (fully verified), `pending` (needs confirmation)
- The primary optimization target uses the `accepted` slice — this is the scientifically cleaner subset
- Use the `all` slice as a shadow diagnostic to catch regressions on pending items

If the benchmark uses a single expected-output file instead of per-project labels, document that path and keep it immutable.

## How to run

```bash
# Raw benchmark, for initial diagnostics only
[benchmark-command] --project [project-id] --artifact-dir artifacts/[run-label]

# Measured autoresearch runs, used after bootstrap
pnpm autoresearch:[slice] --baseline
pnpm autoresearch:[slice] --hypothesis "..." --file path/to/mutable-file.ts
```

After bootstrap, measured experiments should go through the autoresearch runner, not the raw benchmark. Use a stable command such as `pnpm autoresearch:[slice]`, `python -m benchmarks.[slice].autoresearch`, or the equivalent repo-native runner so artifacts, program snapshots, guardrail decisions, and ledger rows stay canonical.

### Output

Each run produces a timestamped artifact directory containing:

| File | Purpose |
|------|---------|
| `run-metadata.json` | Run parameters, git hash, hypothesis, file paths |
| `summary.json` | Full metrics breakdown by project, segment, slice, field |
| `scorecard.json` | Consolidated keep/discard recommendation with guardrail results |
| `mismatches.md` | Human-readable failure summary — start here for debugging |
| `mismatches.json` | Machine-readable mismatch details with field-level disagreements |
| `program.md` | Copy of the program file at run time (audit trail) |

Hard failures still count as loop data. The runner should write metadata, an artifact directory, and a `decision=error` ledger row for timeouts, schema failures, or benchmark crashes.

## Projects

Each project in `projects/` represents a distinct dataset:

| Project | Description | Entries | Status |
|---------|-------------|---------|--------|
| [id] | [what this dataset covers] | [N] | [review status] |

## Autoresearch

The autoresearch scaffold lives in `autoresearch/`. See `autoresearch/program.md` for the optimization objective and constraints.

To run the optimization loop:
```
/autoresearch [path-to-program.md]
```
```

---
