# Benchmark description template

Use the existing benchmark documentation when one exists. Fill only the fields
needed to make this measurement reproducible; the layout and command are examples
of responsibilities, not a framework requirement.

````markdown
# [Behavior] evaluation

## Question and evidence

- Decision supported: [question and intended use]
- Population, unit and sampling window: [definition]
- Cases and provenance: [manifest, source artifacts, real/synthetic distinction]
- Reference truth: [label source, review status, ambiguity policy]
- Splits: [grouping, deduplication, development/validation/final-test manifests]
- Exposure boundary: [who can inspect each split; already exposed data]

## Scoring

- Objective and direction: [metric definition, units, numerator/denominator]
- Guardrails: [quality/cohort floors, latency/cost limits]
- Grader: [assertions or calibrated rubric; version and calibration evidence]
- Failures and exclusions: [retry, timeout, refusal, truncation and missing-case policy]
- Aggregation and uncertainty: [independent unit and paired comparison method]
- Limitations: [what this sample and measurement cannot establish]

## Run and inspect

- Baseline command: [actual repo-native command]
- Experiment command: [actual command with candidate and hypothesis]
- Final confirmation command: [locked original baseline vs selected candidate]
- Resume behavior: [keys, version checks and attempts policy]
- Artifacts: [per-case outputs, traces/end states, grades, usage and summary paths]
- Ledger: [all runs, including errors and rejected experiments]
- Program: [actual path to the experiment contract, if optimization is requested]

## Baseline

[Measured counts, completion, failures, scores by relevant slice, uncertainty,
requested latency/cost metrics and evidence paths. Mark unmeasured fields unknown.]
````
