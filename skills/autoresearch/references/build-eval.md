# Build an eval

The deliverable is a measurement someone can rerun and interrogate. Work from
existing evidence, asking only for consequential gaps. The sections below describe
decisions and proof obligations, not a required interview script.

## Frame the question

Inspect the actual product path, relevant prompts/tools, tests, traces and available
artifacts. Identify the input, intermediate decisions and observable outcome. A
support router's success might mean the destination accepted the ticket, rather
than a plausible team name appearing in the answer.

Write a compact evaluation contract in the repo's existing home:

- The population and unit being evaluated, including exclusions and time window.
- What counts as success, where the reference truth comes from, and how ambiguity
  or abstention is treated.
- The objective, metric direction, denominator, guardrails and useful slices.
- The intended decision: diagnosis, regression detection, model selection or a
  bounded optimization. Note what the metric cannot establish.

Choose measurements from the consequence of error. For asymmetric classification,
a confusion matrix and class-level precision/recall reveal what accuracy hides;
compute them from pooled counts, not by averaging per-case precision. For cost
reduction, quality is a constraint and cost per completed unit is the objective.
Keep conditional accuracy and completion/failure rates visible together.

## Assemble evidence before inventing examples

Sample the real traces, tickets, documents or repository fixtures available within
the task's access. Track provenance and reference-label review status. Cover common
work, known failures, meaningful cohorts, and cases where the desired action should
*not* occur. If oversampling rare failures, report the challenge set separately or
use explicit population weights; its average is not production accuracy.

Use synthetic cases to probe uncovered boundaries or bootstrap a new feature when
real traffic is absent. Mark them and their assumptions; a synthetic pilot cannot
establish a production rate. Candidate-generated answers are proposals for review,
not independent gold. Pending or ambiguous labels remain diagnostic until resolved.
For complex inputs, make source and label inspectable side by side.

Before tuning, split by the source of dependence (customer, conversation, document,
template family, or time), deduplicate across splits, and store a stable manifest.
The development set is readable by the optimizer; validation supports selection;
the final test stays outside its reading/search context until selection is frozen.
If the same agent has already inspected all cases while building the eval, use a
fresh optimizer with scoped access or reserve newly collected cases; renaming a
subset does not erase exposure.
With few independent cases, prefer a modest pilot and an explicit evidence limit
to arbitrary split percentages and a misleading confidence interval.

## Choose a grader by the decision it must make

| Evidence needed | Suitable grader | Calibration question |
|---|---|---|
| Objective label, value, schema, calculation or state change | Deterministic assertion against independent truth | Does it accept valid alternatives and reject meaningful wrong answers? |
| Relative quality of open-ended outputs | Blinded pairwise judgment with randomized order and tie/neither outcomes | Do reviewed comparisons agree, including after swapping order? |
| Absolute semantic requirements | Explicit rubric with evidence for each criterion | Does it reject plausible but incomplete or fabricated work? |
| Unsettled domain judgment | Human review of a small informative sample | Which disagreements change the meaning of success? |

Combine graders when the job has both objective and subjective dimensions. A model
judge gets the relevant evidence, not candidate identity or outcome instructions
embedded in an output. Validate structured grades. Calibrate on independently
reviewed passes, failures and borderline cases, record disagreements and test known
bad/no-op outputs. Avoid using the tested configuration as its own sole judge;
using another provider alone does not prove independence or sound judgment.

For agents, grade the resulting files, tests, records or simulated environment.
Use the trace to assess required process and explain failure, not to substitute a
claim of completion for completion. If pairwise scoring will drive repeated rounds,
freeze the reference artifacts and rubric so each win rate retains its meaning.

Show a small graded pilot with input, actual output/artifact, expected behavior and
reasoning. Resolve disagreements that would alter selection. An already established
rubric and independent labels can supply this evidence without another approval.

## Make the measurement executable

Extend the nearest useful runner rather than replacing it with a prescribed SDK.
The runner accepts versioned cases and a candidate configuration, executes the
product path, captures evidence, grades it, and derives a report from durable
results. A library call, service call, subprocess or CLI is equally acceptable.
Keep expected answers out of the tested system's context and accessible workspace.

Implement the relevant [measurement contract](measurement-contract.md). Prove it
with a small run before scaling: verify one input reaches the actual candidate,
its persisted output is the one graded, and at least one intentionally wrong output
fails. Check failure and resume paths where they can change the verdict or bill.
Use an isolated replay for tool side effects. Reused recordings measure the grader
or harness; they are not fresh evidence about a changed model or prompt.

Make the baseline reproducible with the original configuration and artifact hashes.
Report case counts, completion, failures, overall and critical-segment scores,
uncertainty, and requested cost/latency measures. Distinguish missing measurements
from zero. Match uncertainty to independent sampling units; repeated calls on one
ticket measure model variability, not new customer coverage.

When establishing an improvement loop, also write the bounded experiment contract
using the existing program or [program template](../templates/program.md). Record
runner command, mutable/immutable surfaces, data split policy, decision thresholds,
resource ceiling and stopping rule. Build-only requests stop at the measured baseline;
continue to hillclimb only when improving the product is within the request.
