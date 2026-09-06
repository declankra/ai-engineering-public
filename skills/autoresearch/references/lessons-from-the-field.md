# Lessons from the field

Gotchas from running autoresearch loops in production. The setup phases in
`SKILL.md` cite these where they apply.

## Phase 1 · Readiness check (interactive)

Running autoresearch without verifiable data is the #1 mistake. The agent will optimize the score, but the score won't mean anything. You'll get 100% on garbage labels and ship something that doesn't work.

Put the work into building a real benchmark first. It's the unglamorous part. It's also the part that makes everything else work.

## Phase 2 · Identify the slice (interactive)

Pick the SMALLEST slice that delivers a core value prop and tests one core risk. Don't try to optimize the whole product. "Intake document field extraction accuracy" is a good slice. "Make the AI better" is not.

## Phase 3 · Define the problem and metric (interactive)

Watch the per-segment breakdown, not just the headline number. In one case, overall accuracy went up 5% while the weakest source segment got WORSE. Without segment-level scoring, that regression was completely invisible. The headline number lies when your data isn't uniform.

Add guardrails for your weakest segments.

## Phase 4 · Build the program file (collaborative)

More agency requires more trust. More trust requires more context. When I first ran the loop, the agent could only edit the prompt. It found a cheap win: few-shot examples on 13 rows. Hit 100%. Didn't generalize at all.

When I widened the mutable surface (configs, tools, schemas) AND gave it design principles AND architectural context, it made real improvements that held across new data.

Widen the surface. Raise the context. That's when the loop gets good.

## Phase 5 · Set up the infrastructure (automated)

If you're reviewing labels manually and it's painful, build a tool. An HTML viewer, a browser-based annotation interface, a structured diff — whatever makes it easy to see what's right and what's wrong at a glance.

Good labels are the foundation of the whole loop. If reviewing them is tedious, you'll cut corners and the labels will be wrong. If the labels are wrong, the loop optimizes toward wrong.

Make the important thing easy.

## Phase 6 · Handoff

Three things the model needs to self-improve:

1. A verifiable, vetted benchmark (you can't skip the manual review)

2. Scaffolding and structure to run the loop (that's what we just set up)

3. Your specific domain knowledge (baked into the program file and design principles — keep updating these)

The models are capable. What they need is structure and your judgment. That's the whole insight.
