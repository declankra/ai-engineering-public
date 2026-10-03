# Measurement contract

These are properties of trustworthy evidence, not a required framework or JSON
schema. Use the product's native runner and storage. Implement missing properties
in deterministic code; keep semantic grading and experiment judgment separate.

## Preserve the identity of an observation

An observation binds the case and split, candidate/configuration version, repetition
and attempt to the exact execution evidence and grade. Persist enough to trace:

| Record | Evidence it carries |
|---|---|
| Case manifest | Stable IDs, source/group, sampling/split policy, input/artifact references, label provenance and review status |
| Run configuration | Original baseline or candidate ID, code/prompt/tool/config versions, runner and grader versions, environment and dataset version |
| Attempt | Case/repetition/attempt IDs, timestamps, status/failure class, output, available conversation/tool events, resulting artifacts |
| Execution identity | Requested provider/model/runtime and observed identity when exposed; configuration actually sent; documented alias or fallback resolution |
| Grade | Objective assertions or semantic judgments, evidence/reasons, grader identity/configuration and parse failures |
| Resources | Native usage fields, duration, retry counts, tool and judge usage; measured or estimated cost with rate source/date |
| Decision | Hypothesis, changed surface, comparison, guardrail evidence, keep/discard/error/inconclusive reason and artifact paths |

Missing provider fields remain unavailable, not fabricated zeroes. Retain native
usage units; normalize only quantities with equivalent meanings. If the serving
path does not attest model identity, limit the claim to that configured endpoint.
Unexpected fallback invalidates a claim about a specific model unless fallback is
explicitly part of the evaluated system.

Capture available inputs, outputs, tool events and end-state evidence from the same
execution. Hidden internal reasoning is neither required nor reconstructed. Store
sensitive traces within the authorized data boundary and use references/redactions
where needed. Human reports must remain linked to the artifacts used by the grader.

## Make failure and resumption visible

- Persist completed work incrementally, keyed by run/candidate, case and repetition.
  Resume only matching dataset, configuration and grader versions; each retry keeps
  its own attempt record. A scoring retry can reuse the saved output without another
  product call. An execution retry is a new observation with new resource usage.
- Define before running which attempts contribute to the score. Mirror production
  retry behavior when evaluating the product, and count all its attempts in cost
  and completion time. Report first-attempt reliability when retries matter.
- Separate task failures, refusals/abstentions, truncation, timeouts, tool failures,
  serving errors and harness/grader failures. A system that never completes is not
  accurate because the empty set of completed tasks has no mistakes.
- Reconcile scheduled, completed, scorable, failed and missing cases. Report an
  end-to-end completion/success denominator as well as any conditional quality
  score. Harness errors invalidate affected comparisons; exclusions are explicit
  and applied symmetrically, never a quiet way to improve the average.
- Bound concurrency, attempts and total case duration, not just socket inactivity.
  Budget admission accounts for in-flight work, retries, tools and grading. Stop
  scheduling when the remaining allowance cannot cover confirmation. Record unknown
  residual spend if a timeout cannot cancel an underlying call; do not promise a
  hard monetary cap the provider cannot enforce.
- Replays isolate external effects and reset relevant state between cases. Snapshot
  the allowed mutation surface so rejected candidates can be undone without losing
  unrelated changes. Check measurement artifacts for unauthorized drift before
  interpreting a round.

## Make comparisons commensurable

Compare the same cases, reference truth, grade definitions and conditions, or record
why a difference is intentional. Preserve the frozen original baseline and separate
it from the advancing incumbent. Grade cached baseline outputs with the same valid
rubric version when possible; rerun if changed execution conditions can affect the
claim. Repair an invalid rubric across every affected candidate, not one side.

Derive summaries from persisted rows. Include counts, denominators, important
slices, uncertainty and failure rates with averages. Aggregate precision/recall from
confusion counts; clustered cases require group-aware intervals. Repetitions improve
estimation of stochastic behavior, not dataset diversity. State whether timing
includes queueing, tools and retries, and whether a cost is billed, estimated or
unavailable. Product cost and evaluation/judge cost answer different questions.

Before trusting a new or imported harness, exercise the failure modes that can
reverse its decision: wrong/reference outputs, candidate identity, leaked labels,
missing cases, retry accounting and interrupted/resumed runs. Choose checks relevant
to the actual path. A static schema check or an attractive report alone cannot
verify any of these behaviors.
