## LOOP RUNNER SKILL TEMPLATE

Use this only when the target repo needs to carry its own local runner command, or when the agent environment cannot use the globally installed/projected `declankra/ai-engineering-public/autoresearch` skill.

Create this file at `.claude/skills/autoresearch/SKILL.md`:

````markdown
---
name: autoresearch
description: Run an autoresearch optimization loop against a program.md file. Use when the user asks to optimize, improve, or run an autoresearch loop on a benchmark.
argument-hint: <path-to-program.md>
---

# Autoresearch Optimization Loop

You are running an autoresearch optimization loop defined by the program file at `$ARGUMENTS`.

## Bootstrap sequence

Do these steps in order before running any experiments:

### 1. Read the program

Read `$ARGUMENTS`. This is the human-owned program specification. It contains:
- The objective and primary metric
- The current baseline numbers
- Required reading (architecture specs, implementation files)
- Mutable and immutable surfaces
- Design principles you must follow
- Guardrails that must not regress
- Experiment directions to try
- Stop conditions

Extract the `AUTORESEARCH_CONFIG` JSON block from the program. You need:
- `primaryMetric` and `targetMetric` — what you're optimizing and the goal
- `baseline` — current numbers to beat
- `guardrails` — constraints that must not regress
- `focusArea` or `focusSegment` — the segment that needs the most improvement, when present
- `mutablePaths` — files you are allowed to change
- `immutablePaths` — files you must not touch
- `requiredReading` — files to read before making changes
- `defaultProjects` — which benchmark projects to run

### 2. Read required context

Read every file listed in `requiredReading`. These explain the architecture and design rationale. Understand *why* things are the way they are before changing them.

Then read every file listed in `mutablePaths` — this is the code you'll be changing.

### 3. Read the gold labels

Look in the directory tree near the program file for gold label files. For each project in `defaultProjects`, find and read the corresponding label file so you understand what correct output looks like.

### 4. Read latest mismatch artifacts

Find the most recent artifact directory under the `artifacts/` folder sibling to the program file. Read:
- `mismatches.md` — human-readable failure summary
- `summary.json` or `scorecard.json` — machine-readable metrics

If no artifacts exist yet, skip this step — you'll generate them on your first run.

### 5. Understand the current failures

Before forming any hypothesis, summarize:
- Which segments are failing and why
- Which fields/categories have the lowest scores
- Whether step logs show tool-use, step-count, or token issues
- What the mismatch artifacts reveal about error patterns

## Experiment loop

For each experiment:

### Form a hypothesis
State clearly what you think is wrong and what change you believe will fix it. Reference the design principles from the program.

Before committing to the hypothesis, check whether it is likely to generalize:
- Prefer changes that should help the broader task class, not just the current benchmark file.
- Be suspicious of deterministic shortcuts that encode the current mismatch pattern, a tiny fixed vocabulary, or special handling for one artifact shape.
- If a deterministic rule mainly exists to make current gold labels pass, treat it as probable overfitting unless the program explicitly calls for deterministic handling.

### Make the change
Edit only files in `mutablePaths`. Do not touch anything in `immutablePaths`. Follow the design principles.

### Run the benchmark
Use the repo's autoresearch runner command from the program file or its parent README. Do not use the raw benchmark command for measured experiments unless the user explicitly asks for an off-loop diagnostic.

If the benchmark or model call hard-fails, keep the failure inside the loop: write/run through the runner so metadata, artifacts, and a `decision=error` ledger row are preserved.

### Evaluate results
Compare against the baseline from the program config:
- Did the primary metric improve?
- Did any guardrails regress?
- Did the focus segment improve?
- What do the step logs show?
- What do the new mismatch artifacts reveal?
- Does the winning change plausibly generalize beyond this dataset?

### Keep or discard
- **Keep** if: guardrails pass AND (target reached OR improved vs baseline)
- **Discard** if: any guardrail failed OR no improvement

If keeping: record the run using the autoresearch ledger command found in the program's parent README.
If discarding: revert the change and try a different approach.

### Iterate
Form the next hypothesis based on what you learned. Continue until stop conditions are met.

## Rules

1. **Read the program's design principles and follow them.** They exist because of real failure modes.
2. **Never edit immutable files.** Gold labels, scorer code, operations layer, workflow, DB schema, and business logic are off limits unless the program explicitly says otherwise.
3. **Check step logs.** They reveal whether the model is using tools, how many steps it takes, and where tokens go.
4. **Focus on the weakest segment.** Do not optimize the average by making the strongest segment stronger.
5. **One hypothesis per experiment.** Do not change multiple things at once.
6. **Do not overfit to the benchmark.** Narrow deterministic fixes that only match the current dataset are usually wrong.
7. **Revert failed experiments cleanly.** Do not accumulate partial changes.
8. **When you hit the stop condition, stop.** Report final results and what worked.
9. **Keep measured runs inside the loop.** Once bootstrap is done, every measured run must go through the autoresearch runner so artifacts, failures, and ledger entries stay canonical.
10. **Hard failures are data.** Timeouts, schema failures, and benchmark crashes should produce visible artifacts and ledger rows instead of disappearing as terminal noise.

## Reporting

After each experiment, briefly report:
- Hypothesis tested
- Change made (which files, what changed)
- Result (primary metric, focus segment metric, guardrails)
- Decision (keep/discard)
- What you learned

When the loop completes, provide a final summary:
- Starting baseline → final numbers
- Which experiments were kept vs discarded
- What made the biggest difference
- Recommended next steps if target was not reached
````

---
