# Loop documentation template

Use this when a new loop needs run instructions distinct from its benchmark docs.
Keep a single document when that is enough.

````markdown
# [Behavior] improvement loop

- Program: [objective, direction, guardrails, allowed changes and stopping rules]
- Measured runner: [actual command]
- Input/split manifest: [versioned path; optimizer reads development data only]
- Grader: [version and calibration evidence]
- Original baseline: [immutable configuration and artifacts]
- Current incumbent: [configuration and the evidence for keeping it]
- Ledger: [append-only experiment history]
- Run artifacts: [per-case output, execution evidence, grade, failure and usage records]

Form a hypothesis from development evidence, snapshot the allowed changes, run the
measured command and apply the declared selection policy to validation results.
Preserve all outcomes. Restore only the experiment's patch on rejection. Confirm a
locked candidate against the original baseline on untouched data before claiming
improvement. Deployment is a separate action.

## Commands

[Actual baseline, candidate, resume and final confirmation commands, plus the
resource allowance, retry semantics and expected artifacts.]

## Current evidence

[Measured bottleneck, uncertainty and next informative experiment.]
````
