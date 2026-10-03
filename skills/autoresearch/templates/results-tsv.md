## RESULTS.TSV

Create the ledger with tab-separated headers. Keep an existing ledger schema. For a new ledger, keep the base audit columns, link the split/configuration/measurement versions and per-case accounting through the artifact directory, then add domain-specific metrics between `segments` and `latency_seconds`.

```
timestamp	run_label	hypothesis	files_changed	projects	segments	[primary_metric]	[diagnostic_1]	[diagnostic_2]	[focus_segment_metric]	latency_seconds	decision	guardrails	artifact_directory	git_head	notes
```

### Column definitions

| Column | Description | Example |
|--------|-------------|---------|
| `timestamp` | ISO 8601 run timestamp | `2026-03-24T14-39-36.702Z` |
| `run_label` | Short human-readable label | `baseline`, `improve-tool-defs` |
| `hypothesis` | What you thought was wrong and what you changed | `Long-form source titles are being truncated because...` |
| `files_changed` | Comma-separated modified files | `ai/task/prompts.ts,ai/task/schemas.ts` |
| `projects` | Comma-separated project/dataset IDs tested | `dataset-a` |
| `segments` | Comma-separated segment filters (empty = all) | `source-type-a` or empty |
| `[primary_metric]` | **Rename to your actual metric** | `0.9649` |
| `[diagnostic_N]` | **Rename to your diagnostics; add/remove as needed** | `0.9194` |
| `[focus_segment_metric]` | **Rename to your focus segment metric** | `0.963` |
| `latency_seconds` | How long the benchmark run took | `193` |
| `decision` | `baseline`, `keep`, `discard`, `error`, or `inconclusive` | `keep` |
| `guardrails` | `pass` or comma-separated list of failures | `pass` |
| `artifact_directory` | Relative path to run's artifact folder | `artifacts/2026-03-24T14-39__baseline` |
| `git_head` | Commit hash at time of run | `f464ddb` |
| `notes` | Optional free-text | `First run after architecture change` |

### Rules

- **Append-only.** Never edit or delete rows. The ledger is an audit trail.
- **Every run gets a row.** Baselines, kept runs, discarded runs, and hard errors all produce rows. Timeout and guardrail-failure rows are useful evidence, not noise.
- **Inconclusive comparisons retain the incumbent.** With an existing ledger that has no such enum, use its supported outcome plus an explicit inconclusive reason; preserve parser compatibility.
- **Baseline runs use `decision: baseline`.** They establish the starting point, not a keep/discard judgment.
- **Guardrail failures use `decision: discard` unless the benchmark itself failed.** Benchmark/runtime failures use `decision: error` with the error message in `notes`.
- **Keep file paths honest.** `files_changed` should name only files actually touched by the hypothesis.

---
