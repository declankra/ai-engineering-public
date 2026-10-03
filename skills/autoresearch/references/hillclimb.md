# Hillclimb against evidence

Improve the outcome within the user's constraints. Continue the existing program
and runner rather than restarting setup or widening the assignment.

## Establish what the comparison can decide

Read the objective, metric direction, guardrails, product economics, allowed changes,
required context and prior runs. With an `AUTORESEARCH_CONFIG` program, retain its
existing fields and runner semantics; additional experiment details can live in
prose or an existing manifest without a schema migration.

Inspect the [measurement contract](measurement-contract.md) and development-case
disagreements. Check independent reference truth and baseline reproducibility. A
candidate-derived reference-match metric measures imitation and cannot select that
candidate. Classify discrepancies as candidate behavior, adapter, label, grader or
harness defects before drawing capability conclusions.

A missing baseline calls for a baseline run; a broken instrument calls for a repair.
Use [build-eval](build-eval.md) for the missing parts. An incomplete or invalid run
counts against the tested configuration's product fitness, but does not establish
that its model lacks the capability. After an instrument repair, version it and
rerun every affected candidate fairly; never change the scorer to rescue a result.

Before optimization, make the decision contract explicit:

- Objective and direction; minimum useful improvement and acceptable regression.
- Quality and cohort floors, latency/cost constraints, and simplicity tradeoffs.
- Mutable paths/configuration, immutable measurement, and the exact runner command.
- A bounded resource allowance (spend, time, requests or rounds), with capacity
  reserved for confirmation and the final comparison.
- Stop on the target, exhausted allowance, a declared no-progress limit, invalid
  measurement, or a consequential unresolved product decision.

Infer technical choices from evidence and existing authorization. Ask only for
missing material constraints. A finite pilot can measure runtime or cost, but an
unknown price is not permission for an unlimited loop.

## Separate learning, selection and confirmation

Development examples and traces drive hypotheses. Validation metrics choose among
candidates under the predeclared policy; repeated selection also overfits validation,
so keep search finite and record how often it was consulted. The untouched test is
for the locked final comparison, not another source of hypotheses.

Enforce this boundary with the available runtime: manifests, isolated evaluation
commands, access scopes, or a separate evaluator. A file called `test` is not hidden
if the optimizer has read its labels or failures. Declare exposure; use new data for
an independent final claim. Existing unsplit benchmarks remain useful for diagnosis,
but improvement on them is provisional until confirmed independently.

For small or noisy differences, inspect paired development disagreements and repeat
comparable runs. Quantify the uncertainty of the *difference* at the independent
case/group level; overlap between two individual confidence intervals is not the
decision rule. A non-significant quality drop does not prove equivalence. "Cheaper
without worse quality" needs a predefined tolerance and evidence that the quality
delta stays within it, including critical cohorts.

## Learn through bounded experiments

Each experiment connects evidence to a falsifiable hypothesis: what failure it
addresses, what changes, and why that should transfer to unseen work. One hypothesis
may require coordinated edits; unrelated ideas get separate measurements.

Snapshot the current candidate and any pre-existing edits. Apply only the authorized
patch, execute the measured runner, then inspect actual per-case artifacts and
critical slices. Preserve every trial, including losses and failed attempts, in the
existing ledger. Never choose the best retry and hide the others.

Keep a candidate only when the selection policy and guardrails are satisfied with
adequate evidence. Revert a rejected experiment's own patch while preserving other
work. Inconclusive means keep the previous working incumbent, or collect the specifically
missing evidence within budget; it does not mean accept the apparently higher number.
The original baseline stays frozen even when the incumbent advances.

An incumbent accepted on validation is provisional. Keep its local working state
distinct from the original baseline or a previously independently confirmed result;
validation acceptance alone is not confirmation or permission to ship.

For a model/API change, verify both the candidate identity and the native request
semantics. Make necessary compatibility edits explicit; distinguish a drop-in model
comparison from a comparison of separately tuned systems. Avoid declaring a model
inferior because its adapter silently dropped tools, structured output or context.

For cost or latency objectives, measure the whole completed task: output length,
reasoning, tool use, retries, failures and caching can reverse token-price rankings.
Explore model/configuration combinations only as supported by the actual interfaces.
Probe plausible quality/cost boundaries; assume neither equal effort labels nor
monotonic improvement with price or effort. Confirm finalists under comparable load
and cache conditions; keep product operating cost separate from judge/search expense.

## Close the experiment

Freeze the selected candidate, grader, case manifest and decision rule, then compare
that candidate and the original baseline on the untouched test. Use paired inputs
and a comparable repeat policy. If confirmation fails, retain/restore the original
baseline or a previously independently confirmed configuration, never the failed
candidate or another validation-only incumbent. Report that the apparent win did
not generalize. Once
final-test feedback informs another change, it is development evidence; a renewed
final claim needs a fresh holdout.

Reconcile scheduled cases against outputs, failures and accounting before reporting.
Explain the absolute before/after outcomes and differences, uncertainty, segment
tradeoffs, total experiment resources and stop reason. Link the patch/configuration,
runner command and run artifacts. State whether the candidate is merely selected,
locally applied or separately deployed; this skill does not grant deployment scope.
