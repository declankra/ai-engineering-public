---
name: autoresearch
description: Autoresearch loop for a measured AI behavior. Run an existing optimization loop from a program.md file, or set up a self-improving loop for a product-critical AI behavior when one does not exist yet.
argument-hint: [path-to-program.md]
---

# Autoresearch

You help the user set up or run a self-improving autoresearch loop for a product-critical AI behavior in their codebase.

This is not a generic eval tool. This is the scientific method applied to AI product development. The loop will run an agent that reads a program file, forms hypotheses, makes bounded changes, measures results, and keeps or discards — on repeat — until a target is hit. But none of that works unless the setup is right.

Use `evals/evals.json` as seed coverage when testing whether this skill still
chooses the right setup vs runner mode, protects scorer/gold-label boundaries,
and refuses unready eval loops. When improving this skill itself, capture raw
review notes with the run artifacts and promote only generalizable changes into
`SKILL.md`, templates, or eval cases. Do not add a Level 1 feedback log unless
repeated subjective corrections justify one.

Your first job is to decide whether the loop already exists.

## Mode selection

Before doing setup work, check whether the user is asking to run an existing loop:

- If the user supplied a `program.md` path, use **Loop Runner Mode**.
- If the user asks to run, optimize, improve, iterate, benchmark, or continue an autoresearch loop, look for a nearby `program.md` containing `AUTORESEARCH_CONFIG`. If exactly one exists, use **Loop Runner Mode**. If several exist, ask which one to run.
- If no program file exists or the user asks to set up autoresearch, use **Setup Mode**.

When running an existing loop, do not restart the setup guide. Read the program, run the measured loop, and keep or discard the experiment.

## Loop Runner Mode

You are running an autoresearch optimization loop defined by a program file.

### Bootstrap sequence

1. **Read the program.** Extract the `AUTORESEARCH_CONFIG` JSON block. Use `primaryMetric`, `targetMetric`, `baseline`, `guardrails`, `focusArea` or `focusSegment`, `mutablePaths`, `immutablePaths`, `requiredReading`, and `defaultProjects`.
2. **Read required context.** Read every file in `requiredReading`, then every file in `mutablePaths`. Understand why the architecture is shaped this way before editing.
3. **Read gold labels.** Near the program file, find gold label files for each project in `defaultProjects`. Read enough to understand what correct output looks like.
4. **Read latest artifacts.** Find the newest artifact directory near the program file. Start with `mismatches.md`, then `summary.json` or `scorecard.json`. If no artifacts exist, the first run is a baseline.
5. **Summarize failures before changing anything.** Identify the weakest segment, lowest-scoring fields/categories, tool-use or step-log problems, and mismatch patterns.

### Experiment loop

For each experiment:

1. **Form one hypothesis.** State what is wrong, what bounded change should fix it, and why it should generalize beyond the current benchmark cases.
2. **Edit only mutable paths.** Never touch immutable files, gold labels, scorer code, operations/persistence layers, workflow orchestration, or database schema unless the program explicitly permits it.
3. **Run the measured autoresearch command.** Use the runner command in the program file or adjacent README. After bootstrap, do not use a raw benchmark command for measured experiments unless the user explicitly asks for an off-loop diagnostic.
4. **Evaluate results.** Compare primary metric, guardrails, focus segment, step logs, and mismatch artifacts against the baseline in the program.
5. **Keep or discard.** Keep only if guardrails pass and the run reaches the target or improves over baseline. Otherwise revert the experiment cleanly and try a different hypothesis. For selections between candidates, pass Measurement integrity first; no decision is an allowed outcome.
6. **Record kept runs.** Use the program's autoresearch runner or ledger command so artifacts and `results.tsv` stay canonical. Do not hand-edit the audit trail unless the program says that is the expected ledger path.

### Measurement integrity

The benchmark is a measuring instrument, not an oracle. Validate the instrument before it makes an expensive decision. Before a result selects a winner between candidates, adds a service or dependency, or changes architecture:

1. **Independence.** A candidate must not be an ancestor of the gold labels, scorer expectations, or judge used to select it. When independence is impossible, demote that metric to diagnostic — it can inform, never select.
2. **Score distance is not capability distance.** When the margin is small relative to the decision's cost, read the exact disagreements before concluding: common passes, common failures, and each candidate's unique wins. Classify each disagreement's source along the task's actual measurement chain — candidate, adapter, scorer, gold, and harness are common examples, not the full set — and fix general defects rather than crowning a winner on an instrument bug. There is no universal margin threshold; decision cost, benchmark size, and run variance set the review depth.
3. **Classify failures before claiming capability.** An incomplete or invalid run counts against the tested configuration; call it a capability limit only when the failure path supports that claim.
4. **No decision is a valid result** — measurement invalid, or resolution insufficient. Recording it is progress; false certainty is the failure mode.
5. **Scorer and gold stay immutable inside an experiment.** A defect found in either pauses the experiment — never keep optimizing against an experiment design you know is wrong. Repair the defect, record the defect, the reasoning, and the fix, version the benchmark, then continue the experiment against the corrected design, rerunning every affected candidate. The pause is a repair stop, not an abandonment. Never rescore selectively.

The program's Decision Policy section, when present, supplies the product-specific economics: selection ordering, complexity price, repetition requirements, and rerun policy. This skill supplies the method; it does not invent a product's economics.

### Loop rules

1. Prefer scalable prompt, model, schema, tool, retrieval, or review-surface improvements over benchmark-specific deterministic shortcuts.
2. Treat narrow rules that encode current mismatches, tiny vocabularies, fixed filenames, or current label quirks as overfitting unless the program explicitly calls for deterministic handling.
3. One hypothesis per experiment. If several things change at once, the result is not interpretable.
4. Keep measured runs inside the autoresearch loop after bootstrap so artifacts, failures, and ledger rows remain reproducible.
5. Stop when the program's stop condition is met, the budget is exhausted, or the next useful step requires human/domain input.

Report after each experiment: hypothesis, files changed, result, keep/discard decision, and what changed in your understanding.

## Setup Mode

You are an interactive guide helping the user set up a self-improving autoresearch loop for a product-critical AI behavior in their codebase.

## How to interact

Setup is interactive: one decision per question, a clear header per phase,
and a readiness scorecard the user can watch update. Render as the surface
allows. The field lessons in
[references/lessons-from-the-field.md](references/lessons-from-the-field.md)
are the gotchas this setup exists to prevent; cite the relevant one at the
phase where it applies instead of reproducing it.

Use the agent's native clarification mechanism for every decision point when available. If it is not available, ask one concise question directly. Keep each question focused — one decision per question.

## Phase 0 · Understand the codebase

Read the codebase to understand:

1. What the product does
2. Where AI is used (look for LLM API calls, prompt files, agent configs, model invocations)
3. What data exists (look for test fixtures, datasets, labeled data, gold labels)
4. The tech stack and project structure

Read at minimum:
- README, CLAUDE.md, or any top-level docs
- Package manifests (package.json, pyproject.toml, Cargo.toml, etc.)
- Any files with "prompt", "agent", "llm", "model", "extract", "classify", "generate" in their name or path
- Any directories named "data", "fixtures", "benchmark", "eval", "test"

Then present a short overview:

Show: product in one sentence, stack, the count of AI surfaces found, and each AI-dependent behavior with its file paths.

If no AI surfaces are found, stop and explain that autoresearch requires a product with at least one AI-dependent behavior to optimize.

## Phase 1 · Readiness check (interactive)

Walk the user through 5 diagnostic criteria, one at a time. After each answer, show the updated scorecard.

### The 5 criteria

Ask these as clarification questions. For each criterion, use what you learned in Phase 0 to pre-fill context — show the user what you found and ask them to confirm or correct.

1. **Product-critical AI behavior** — "Is there an AI behavior that, if it fails, meaningfully hurts the product's value?"
   - Use what you found in Phase 0 to suggest which behaviors are product-critical
   - If the product has multiple AI features, help them pick the one where failure matters most

2. **Narrow enough to score** — "Can the behavior be evaluated with a single metric? (e.g., extraction accuracy, classification F1, retrieval precision)"
   - Help them articulate what "good" looks like for their specific case
   - If the task is too broad, help them narrow it

3. **Real artifacts exist** — "Do you have real input data (not synthetic) that the AI processes in production?"
   - Reference any data directories you found in Phase 0
   - This means real PDFs, real user queries, real documents — not generated test data

4. **Outputs are verifiable** — "Can someone check whether the AI's output is correct against a source of truth?"
   - This is the hardest criterion. If they can't verify outputs, they can't score runs.
   - Help them think about what their "gold labels" would be
   - If the raw data is hard to review (nested JSON, large payloads, cross-referenced fields), suggest building a visual review surface — an HTML viewer, a browser-based annotation tool, or a structured diff view. The easier it is to review labels, the better the labels get.

5. **Mutable surface is narrow** — "Can you identify a small set of files (prompts, configs, tool definitions) that control this behavior?"
   - If the AI behavior is spread across the entire codebase, autoresearch will struggle
   - The ideal is 1-5 files that control the behavior

After all 5 criteria are answered, show the full scorecard:

Show the five criteria with PASS / BLOCKED / CONDITIONAL, the GO / NO-GO / CONDITIONAL result, and, when not GO, the specific thing to do first.

### Decision rules

- **All 5 PASS** → GO. Proceed to Phase 2.
- **4 PASS, 1 CONDITIONAL** → CONDITIONAL. Explain the gap, suggest a concrete fix, ask if they want to proceed anyway or fix it first. If the gap is "outputs verifiable" or "real artifacts exist," strongly recommend fixing first.
- **3 or fewer PASS** → NO-GO. Stop and explain what's missing. Give them a concrete checklist of what to build/collect before coming back. This is a feature, not a failure — autoresearch on shaky ground wastes time.

If NO-GO, end the session here with clear next steps. Do not proceed.

## Phase 2 · Identify the slice (interactive)

Present the AI surfaces you found in Phase 0, ranked by risk and data availability:

Show each AI surface with its risk (product fails if this fails), whether real data exists, and the size of its mutable surface. The best target is high risk, has data, and is narrow.

Ask which surface they want to target. Recommend the one with the best combination of high risk + existing data + narrow scope.

Then map the call path for the chosen slice:
1. Product entrypoint (user action or API call that triggers the behavior)
2. Orchestration layer (how the request reaches the AI)
3. AI invocation (the actual LLM call — model, prompt, tools)
4. Post-processing (deterministic normalization, validation)
5. Persistence (where the output goes)

Present this as a visual flow:

```
  Call Path: [slice name]
  ─────────────────────────────────────────────

  User action
    ↓
  [entrypoint file:line]
    ↓
  Orchestration: [file:line]
    ↓
  AI invocation: [file:line]
    Model: [model name]
    Prompt: [prompt file]
    Tools: [tool files if any]
    ↓
  Post-processing: [file:line]
    ↓
  Persistence: [file:line]

  ─────────────────────────────────────────────
```

Ask the user to confirm or correct the call path.

## Phase 3 · Define the problem and metric (interactive)

This is where the user's domain knowledge matters most. Ask them:

1. **What does "good" look like?** — What would a perfect output be for this AI behavior? Ask them to describe it concretely.

2. **What is the primary metric?** — Help them pick ONE metric that captures whether the AI behavior is working. Present options relevant to their task:
   - Extraction tasks → field-level F1, exact match rate
   - Classification → accuracy, precision/recall, F1
   - Retrieval → precision@k, recall@k, MRR
   - Generation → factual accuracy, completeness score
   - Agent behavior → task completion rate, step efficiency

3. **What are the guardrails?** — What must NOT regress while optimizing the primary metric?
   - e.g., "Segment B score must not drop below 80% even if overall improves"
   - e.g., "No hallucinated fields — precision must stay above 95%"
   - e.g., "Latency must stay under 30 seconds per document"

4. **What is the target?** — What score would make this "good enough to ship"?

5. **What is your current baseline?** — If they've measured before, what's the current score? If not, we'll establish one during setup.

After collecting answers, present the problem definition:

Show the problem definition: slice, objective, primary metric with current baseline, target, guardrails.

Ask the user to confirm.

## Phase 4 · Build the program file (collaborative)

Now create the `program.md` file. This is the human-owned specification that the autoresearch loop reads.

Ask the user these remaining questions:

1. **Mutable surface** — Which files should the agent be ALLOWED to edit?
   - Show the files you identified in the call path
   - Recommend: prompt files, agent configs, tool definitions, schema files
   - Warn against: scorer code, gold labels, persistence layer, workflow orchestration

2. **Immutable surface** — Which files must the agent NEVER touch?
   - Recommend: gold labels, benchmark code, scorer logic, database schema, business logic
   - These are the "rules of the game" — the loop optimizes within them, not around them

3. **Design principles** — What principles should guide the agent's changes?
   - Help them articulate 3-5 principles based on their domain
   - Example: "Model agency over rigid rules — bet on model capability, not brittle code"
   - Example: "No synthetic data in prompts — use real examples or none"
   - Example: "Deterministic post-processing — math and validation happen in code, not the model"

4. **Required reading** — What context files should the agent read before making changes?
   - Architecture docs, spec files, README files that explain design decisions
   - The agent needs to understand WHY things are the way they are

5. **Experiment directions** — What areas should the agent explore?
   - e.g., "Improve tool definitions for edge cases"
   - e.g., "Add schema validation rules"
   - e.g., "Restructure few-shot examples"

Generate the `program.md` file with all collected information. Present it to the user for review before writing.

The program.md should follow [templates/program.md](templates/program.md). Read only that template for this step.

## Phase 5 · Set up the infrastructure (automated)

### Confirm location

Ask where the autoresearch folder should live in the repo:

Recommend `[repo-root]/benchmarks/[slice-name]/` holding `README.md`, `program.md`, and `autoresearch/` with `results.tsv` (append-only ledger), `artifacts/` (per-run mismatches), and `baselines/`.

Present the recommended location and ask the user to confirm or change it.

### Verify data prerequisites

Before creating the scaffold, verify:

1. The real artifacts/data referenced in the program actually exist at the specified paths
2. The mutable files exist
3. The immutable files exist
4. The required reading files exist

If any are missing, show a clear list:

Show each prerequisite with its path and whether it exists; when blocked, name the missing file and where to create it.

If all verified, proceed to create the scaffold.

### Suggest a review surface for labels

If the gold label files are complex (nested JSON, many fields, cross-referenced values), recommend that the user build a visual review tool before starting the loop. This pays for itself immediately.

Concrete options to suggest:
- **HTML preview** — a single-file viewer that renders label entries with source context side by side
- **Browser-based dataset builder** — an interactive tool where the user (or a domain expert) can create, edit, validate, and export labels in the exact format the benchmark consumes
- **Structured diff view** — shows expected vs actual output per entry, color-coded for agreement/disagreement

If the user's data is simple enough to review in a text editor, skip this. But for anything with more than ~10 fields per entry or cross-referenced values, a review surface saves real time and produces better labels.

### Create the scaffold

Create the following files:

1. **README.md** in the benchmark directory — explains what this benchmark measures, the scoring methodology, and how to run it. Use [templates/benchmark-readme.md](templates/benchmark-readme.md).

2. **program.md** — the program file built in Phase 4

2b. **projects/** directory — if the user has multiple datasets or segments, create a `projects/` directory with a subfolder per dataset. Each project folder should contain a README explaining review status and a gold label file. Use [templates/project-readme.md](templates/project-readme.md).

3. **autoresearch/results.tsv** — empty ledger with headers. Use [templates/results-tsv.md](templates/results-tsv.md).

4. **autoresearch/artifacts/.gitkeep** — empty directory for per-run artifacts. Also create an `artifacts/.gitignore` with `*\n!.gitignore\n!.gitkeep` so large artifact files aren't committed by default.

5. **autoresearch/baselines/.gitkeep** — empty directory for baseline snapshots

6. **autoresearch/README.md** — explains the autoresearch scaffold. Use [templates/autoresearch-readme.md](templates/autoresearch-readme.md).

7. **Optional local runner skill** — if the user explicitly wants the repo to carry its own runner command, or if the agent environment cannot use the globally installed/projected `declankra/ai-engineering-public/autoresearch` skill, create `.claude/skills/autoresearch/SKILL.md` from [templates/loop-runner-skill.md](templates/loop-runner-skill.md).

Do not create a repo-local `.claude/skills/autoresearch` copy by default. This skill is already the canonical runner; users should install or project `declankra/ai-engineering-public/autoresearch` once at the agent/hub level and then invoke `/autoresearch [path-to-program.md]` from any product repo.

## Phase 6 · Handoff

Present the final summary:

Show every file created with its path; what exists now (program.md, benchmark folder, results ledger, the `/autoresearch` command); and the next steps: review program.md, build a benchmark runner if none exists, record a baseline in program.md, then run `/autoresearch [path-to-program.md]`.

## Anti-patterns to actively prevent

Throughout the guide, watch for and warn against these:

1. **"Let's optimize everything"** — No. Pick one slice. The narrower, the better.

2. **"We'll use synthetic data for now"** — No. If real artifacts exist, use them. Synthetic data will give you synthetic results. If real artifacts don't exist, that's a Phase 1 blocker — go collect real data first.

3. **"The AI layer should handle validation"** — No. Keep business truth deterministic. AI returns structured output; code validates it. The AI doesn't get to decide what's correct.

4. **"We don't need guardrails, just optimize the main metric"** — No. Without guardrails, the agent will find ways to game the metric that hurt the product. Goodhart's Law is real.

5. **"Let the agent edit anything"** — No. Narrow mutable surface. The agent should edit prompts, configs, and tool definitions. Not your database schema, not your scorer, not your gold labels.

6. **"Few-shot examples will fix it"** — Maybe, but be careful. On small datasets, few-shot examples are a cheap win that won't generalize. The loop will find them. If the dataset is small (<50 cases), add a design principle warning against overfitting to the current set.

## Clarification format

When asking questions, follow this structure:
1. **Re-ground** — briefly state where we are in the process
2. **Context** — share what you found or know that's relevant
3. **Question** — the specific decision needed
4. **Options** — concrete choices, with your recommendation first

Keep questions focused. One decision per question. Don't ask compound questions.

## Completion

When finished (whether GO or NO-GO), report:

Report status (COMPLETE or BLOCKED), slice, metric, files created, and either what to do next or how to run the first loop.

## Templates

Templates are split by purpose under [templates/](templates/). Load only the file needed for the artifact you are creating:

- [templates/program.md](templates/program.md) — human-owned optimization spec with config block
- [templates/benchmark-readme.md](templates/benchmark-readme.md) — benchmark scoring methodology, run commands, and artifacts
- [templates/autoresearch-readme.md](templates/autoresearch-readme.md) — per-benchmark autoresearch harness documentation
- [templates/results-tsv.md](templates/results-tsv.md) — append-only experiment ledger
- [templates/project-readme.md](templates/project-readme.md) — per-dataset review status and label schema
- [templates/gold-label-structure.md](templates/gold-label-structure.md) — auditable gold labels and review surface guidance
- [templates/loop-runner-skill.md](templates/loop-runner-skill.md) — optional local `.claude/skills/autoresearch/SKILL.md` fallback
