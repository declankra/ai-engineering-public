## PROGRAM.MD

The program file is the human-owned specification that controls the entire autoresearch loop. The agent reads this before every experiment. It defines what to optimize, what constraints to respect, and what files the agent can touch.

This shape comes from production loops where the AI behavior was narrow, benchmarked against real artifacts, and optimized through measured experiments. Keep the objective and guardrails human-owned, put required reading near the top, keep labels/scorers immutable, and treat the config block as the runner contract.

```markdown
# Autoresearch Program: [Slice Name]

This Markdown file is human-owned. The agent may read it, but changes to the objective, guardrails, baseline, and mutable surface should come from a human decision.

## Required Reading

Before making changes, read these files:

1. `[path/to/spec]` — [what it covers]
2. `[path/to/architecture-doc]` — [what it covers]
3. `[path/to/implementation-file]` — [what it controls]
4. `[path/to/gold-labels-or-expected-file]` — gold labels or expected output, read-only

## Objective

[1-2 sentences: what AI behavior we're improving and why it matters to the product]

## Primary Metric

`[metric_name]` — [definition of what this metric measures]

- Current baseline: [score or "pending first run"]
- Target: [threshold]

### Shadow Diagnostics

Track these alongside the primary metric for deeper visibility. They don't gate keep/discard decisions, but they reveal problems the headline number hides.

- `[diagnostic_1]` — [what it measures]
- `[diagnostic_2]` — [what it measures]
- Per-segment breakdowns (e.g., per-source, per-cohort, per-category, per-document-type)
- Step logs: tool usage, step count, token consumption (if using agents)
- Hard-failure rows: timeout, runner error, or schema failure should still produce artifacts and a ledger row

## Guardrails

These must not regress. If any guardrail fails, the experiment is discarded regardless of primary metric improvement.

| Guardrail | Threshold | Why |
|-----------|-----------|-----|
| [name] | >= [value] | [what breaks if this regresses] |
| [name] | >= [value] | [what breaks if this regresses] |
| [name] | >= [value] | [what breaks if this regresses] |

## Decision Policy

Human-owned economics for selection decisions. The loop supplies method; this section states what this product will pay and when a comparison may decide.

Anchor cost materiality to the value the product delivers and the price charged for it, never to zero. A unit-cost delta that cannot dent that value is immaterial where accuracy is trust-critical; reserve frugality for the places the product is allowed to be less perfect.

- **Ordering:** [what wins when candidates tie — e.g., trust > quality > simplicity > latency > cost]
- **Complexity price:** [what a new service, dependency, or second arm must prove before adoption — a score margin alone never buys it]
- **Close-result trigger:** [when a margin forces disagreement inspection instead of selection — set relative to decision cost, not a fixed percentage]
- **Oracle provenance:** [where gold and scorer expectations come from; any metric a candidate helped produce is diagnostic-only]
- **Repetitions:** [runs a selection requires — e.g., stability across N repeats, not one passing sample]
- **Valid outcomes:** winner · tie · no decision (measurement invalid) · no decision (resolution insufficient)
- **Rerun policy:** [which changed dependencies invalidate a stored proof; benchmark spend buys durable information — never rerun for ceremony, never skip a needed rerun for cost]

## Design Principles

These guide how the agent should think about changes. They come from real failure modes — not theory.

1. **[Principle name]** — [explanation]. [Why this matters: what went wrong without it]
2. **[Principle name]** — [explanation]. [Why this matters]
3. **[Principle name]** — [explanation]. [Why this matters]

Examples of good design principles:
- "Model agency over brittle rules" — real-world source artifacts vary by author, format, and context; prefer improving prompts, tools, schemas, or retrieval over hard-coded rules that only fit the current sample.
- "Deterministic code for objective transforms, model for judgment" — math, normalization, validation, and persistence belong in code. The model handles ambiguity, identity, classification, and extraction.
- "Respect architecture layers" — AI returns structured output → deterministic normalization/validation runs → operations layer persists. The agent must not bypass layers just because a shortcut improves the benchmark.
- "Prefer the simplest generalizable fix" — a small deterministic cleanup plus prompt guidance can outperform a heavier verifier if it improves the target metric without adding latency or complexity.
- "Latency is a product metric when the path is synchronous" — quality-only wins may not be acceptable if the user-facing path blocks on the model call.

## Mutable Surface

Files the agent may edit during experiments:

- `[path/to/file]` — [what this file controls]
- `[path/to/file]` — [what this file controls]
- New files under `[directory/]` if justified by the hypothesis

Keep this narrow. The tighter the mutable surface, the more interpretable each experiment is.

## Immutable Surface

Files the agent must NEVER touch. These are the rules of the game.

- `[path/to/gold-labels]` — benchmark truth, not up for debate
- `[path/to/scorer]` — scoring logic must stay deterministic
- `[path/to/run-benchmark]` — benchmark execution semantics
- `[path/to/business-logic]` — operations/persistence layer
- `[path/to/workflow]` — orchestration, not the agent's concern
- Architecture layer boundaries (AI returns structured output → deterministic code validates → persistence)

## Experiment Directions

Areas to explore, roughly ordered by expected impact:

1. [direction] — [why this might help, what evidence suggests it]
2. [direction] — [why this might help]
3. [direction] — [why this might help]

These are starting points, not a fixed plan. The agent should update this list based on what it learns from each experiment.

## Stop Conditions

- `[primary_metric]` >= [target] AND all guardrails pass
- [N] consecutive experiments with no improvement
- Any guardrail violation that can't be resolved without violating design principles
- Budget exhausted (if applicable)

## Current Baseline

[Leave blank until first benchmark run. After running, record:]

| Metric | Score |
|--------|-------|
| [primary_metric] | [value] |
| [diagnostic_1] | [value] |
| [diagnostic_2] | [value] |
| [guardrail_1] | [value] |
| [guardrail_2] | [value] |

Recorded: [date] | Artifact: `[path-to-baseline-artifact]`

<!-- AUTORESEARCH_CONFIG_START -->
```json
{
  "version": 2,
  "domain": "[domain-name]",
  "task": "[specific-task-identifier]",
  "primaryMetric": "[metric_name]",
  "targetMetric": [target_number],
  "defaultProjects": ["[project-id]"],
  "expectedFile": "[optional path/to/expected-output.json]",
  "focusArea": "[weakest segment — e.g., a specific source, cohort, category, document type, or latency]",
  "baseline": {
    "[metric_name]": [value],
    "[diagnostic_1]": [value],
    "[guardrail_metric_1]": [value]
  },
  "guardrails": {
    "max_[metric]_regression": [value],
    "min_[metric]": [value]
  },
  "mutablePaths": [
    "[path/to/file]"
  ],
  "immutablePaths": [
    "[path/to/file]"
  ],
  "requiredReading": [
    "[path/to/file]"
  ]
}
```
<!-- AUTORESEARCH_CONFIG_END -->
```

### Config block notes

The `AUTORESEARCH_CONFIG` JSON block is machine-readable. The loop runner extracts it to configure each experiment. Key fields:

- `version` — schema version for forward compatibility
- `domain` — what area this covers (e.g., "extraction", "classification", "retrieval")
- `task` — specific task identifier (e.g., "document-extraction-quality")
- `primaryMetric` / `targetMetric` — what to optimize and the goal
- `defaultProjects` — which datasets or projects the runner should measure by default
- `expectedFile` — optional expected output file for benchmarks that do not use per-project gold labels
- `baseline` — current numbers to beat (updated after each kept experiment)
- `guardrails` — constraints with thresholds (keys should match guardrail names)
- `focusArea` or `focusSegment` — the weakest segment that needs the most attention
- `mutablePaths` / `immutablePaths` — what the agent can and cannot edit
- `requiredReading` — files the agent must read before making changes

---
