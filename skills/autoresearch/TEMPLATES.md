# Autoresearch Templates

Templates are split by purpose so agents only load the scaffold they need. Adapt them to the target repo and domain; they are structures, not fill-in-the-blank forms.

These templates are generalized from production autoresearch loops across document extraction, structured row selection/classification, and synchronous import quality/latency. Use the smallest useful subset: human-owned `program.md`, narrow mutable surface, immutable labels/scorers, measured runner command, timestamped artifacts, copied program snapshot, and an append-only ledger with `baseline`, `keep`, `discard`, `error`, and explicitly inconclusive outcomes.

| Template | Use when |
|----------|----------|
| [program.md](templates/program.md) | Creating the human-owned optimization spec and `AUTORESEARCH_CONFIG` block |
| [benchmark-readme.md](templates/benchmark-readme.md) | Documenting what the benchmark measures, scoring, run commands, and artifacts |
| [autoresearch-readme.md](templates/autoresearch-readme.md) | Documenting the per-benchmark autoresearch harness and workflow |
| [results-tsv.md](templates/results-tsv.md) | Creating the append-only experiment ledger |
| [project-readme.md](templates/project-readme.md) | Documenting each dataset/project and its review status |
| [gold-label-structure.md](templates/gold-label-structure.md) | Designing auditable gold label files and review surfaces |
| [loop-runner-skill.md](templates/loop-runner-skill.md) | Portable pointer when a project needs a local entry point |
