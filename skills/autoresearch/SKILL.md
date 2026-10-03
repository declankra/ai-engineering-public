---
name: autoresearch
description: Build trustworthy evals and improve AI behavior against them. Use to turn a product question into a runnable eval, hillclimb quality, cost or latency, compare models or APIs, or continue a program.md loop. Do NOT use for an ordinary bug fix, an unmeasured prompt rewrite, or a generic performance benchmark with no AI behavior.
---

# Autoresearch

Turn a question about an AI product into evidence, then use that evidence to
improve the product. Success is a trustworthy answer or a demonstrated improvement
on unseen work; a larger score on familiar examples is only a diagnostic.

This skill is independent of the agent running it, the model being evaluated,
and the interface used to call that model. An SDK, HTTP endpoint, local process,
CLI, or existing test harness can all serve as the execution path. Adapt to the
product's actual interface; no particular provider, language, framework, model,
or reasoning setting is required.

## Choose the work

- **Build an eval:** `build-eval <question>`, "how often does this work?", or a
  request to establish measurement. Read [build-eval](references/build-eval.md).
- **Improve against an eval:** `hillclimb <goal>`, a supplied `program.md`, or a
  request to run/continue a measured loop. Read [hillclimb](references/hillclimb.md).
  Inspect the existing measurement before changing the product. If it is missing
  or invalid, build or repair it as the first part of the requested work.
- **Existing loops:** retain their runner, config schema, artifact layout and
  ledger. A nearby `AUTORESEARCH_CONFIG` block is an existing contract, not a
  prerequisite for using this skill. Locate the loop from the user's target;
  clarify only if multiple plausible targets remain.

Natural language works too. For example:

```text
/autoresearch build-eval How often does our support bot route to the right team?
/autoresearch hillclimb Reduce cost per resolved ticket while routing quality holds.
/autoresearch benchmarks/routing/program.md
```

## Principles that govern decisions

**The product question owns the metric.** Define the unit of work, intended
population and consequence of failure before choosing a score. Select an objective
and keep quality floors, important cohorts, cost and latency visible separately.
A proxy earns its place by agreement with the outcome it stands for.

**Validate the measuring instrument before optimizing.** Labels, graders, case
selection and the execution path can be wrong. Inspect disagreements along that
whole chain. A runnable script is necessary but does not establish validity.
Choose the least expensive grader that can actually distinguish acceptable from
unacceptable work, and calibrate judgment against independently reviewed examples.

**Generalization is the result.** Use development cases to learn, validation to
select, and an untouched final test to make a claim. Separate related documents,
users or conversations together so near-duplicates cannot cross the boundary.
Limit the claim when there is too little independent data; do not invent certainty.

**Change the system, preserve the experiment.** One interpretable hypothesis may
change prompts, skills, tools, retrieval or model configuration within the agreed
surface. Fix the data, rubric, scorer and selection policy for that experiment.
A measurement defect opens a separately recorded repair and a new benchmark
version; rerun all affected comparisons before selecting a winner.

**Model choice is an empirical result.** Compare the complete configurations the
product could actually run. Equal names for effort, tokens or caching do not imply
equal semantics across providers. Consult current official docs when adapting an
interface, preserve its native evidence, and measure quality and total task cost.
Neither a cheaper token nor a newer model establishes a better product choice.

**Judgment proposes; code measures.** Interpreting failures, choosing criteria and
forming hypotheses are latent work. Splits, state capture, accounting, scoring of
objective facts, aggregation and rollback boundaries are deterministic work.
Reuse or build these in the target repo's idiom; avoid a universal adapter layer
unless a real integration needs it.

**Spend information deliberately.** Derive the experiment scope, budget and stop
conditions from the user's request and existing program. Spend more evidence on
close, costly decisions. A tie, invalid measurement or unresolved uncertainty is
an honest outcome. Improvements that buy negligible value at substantial added
complexity may lose under the product's decision policy.

## Operating boundaries

Use code, traces and existing records to answer technical questions. Ask the owner
for unresolved product tradeoffs or judgments that examples cannot settle. Show
concrete inputs, outputs and grades when asking about the rubric. Existing
explicit labels, policy and session authorization count; do not require a fresh
ceremony. Until material criteria are settled, label pilots provisional and keep
them out of autonomous selection.

Prepare the runnable path and a bounded plan before asking for any missing spend,
data-transfer or external-action authorization. Evaluation is not permission to
send messages, mutate live customer state or deploy a winner. Replay side effects
in an isolated environment. Honor authorization already provided.

Before measured runs in either mode, use
[measurement contract](references/measurement-contract.md) to check the evidence
path. Its requirements are semantic; keep an existing compatible schema.

## Done means

- **Build-eval:** a documented question, sourced cases, reviewed scoring contract,
  runnable command and inspected baseline with per-case evidence. If live execution
  or domain review is unavailable, say exactly which part is implemented and which
  part remains unverified; continue independent setup work.
- **Hillclimb:** a ledger of every experiment, a restored baseline or selected
  candidate, guardrail results and an untouched-test comparison against the original
  baseline. Distinguish local selection from deployment. If uncertainty wins, retain
  the original baseline or a previously independently confirmed configuration and
  report no demonstrated improvement.

Report the outcome, absolute numbers and changes, important segment regressions,
uncertainty, stop reason and paths to the evidence. Result proof is the measured
outcome; path proof connects the exact configuration, execution, artifacts and
grading that produced it. Never reconstruct traces in a separate generation.

## Supporting material

Read [field lessons](references/lessons-from-the-field.md) when readiness,
segmentation, mutable scope or label review is the bottleneck. Use
[templates](TEMPLATES.md) only when a new artifact is needed; an empty scaffold
is not an eval. The program template preserves the existing `AUTORESEARCH_CONFIG`
format without requiring it of other harnesses.

For changes to this skill, exercise [evals](evals/evals.json) against actual
artifacts in a fresh context and judge results separately. Retain task-specific
findings with the run; promote only demonstrated general lessons into the skill.
The upstream inspiration and deliberate departures are recorded in
[sources](references/sources.md).
