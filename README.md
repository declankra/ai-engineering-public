# AI Engineering — Public Skills

**Encoded learnings from building AI products in an AI-native way, turned into
skills you can hand to an agent.**

I work as an applied AI consultant. The full operating system behind that work -
the curated, evaluated, continuously maintained skill corpus I use every day -
stays private. This repo is the public layer: when a method survives real
deployments and measured results, the sanitized version lands here so you can
install it and use it.

Broader context and write-ups live at [Kramper Engineering](https://kramperengineering.com) and
[declankramper.com/writes](https://declankramper.com/writes).

## Skills

### [autoresearch](./skills/autoresearch/)

The scientific method applied to AI product development: a measured experiment
loop for a product-critical AI behavior. Real inputs, trusted gold labels, one
primary metric, bounded changes, keep-or-discard decisions - on repeat until a
target is hit.

Where it came from: a production loop that improved vendor quote extraction
from 76% to 97% on real data. The full story, including where it failed:

- [Building a Self-improving Agent Loop for AI Quote Extraction](https://declankramper.substack.com/p/building-a-self-improving-agent-loop)
- [Encoding and Automating Implicit Knowledge](https://declankramper.com/writes/encoding-and-automating-implicit-knowledge)

These results are origin evidence for the method, not a claim that installing
the skill reproduces them automatically.

## Quick Start

Copy this to your agent (Claude Code, Codex, or similar) to set up a new loop:

```text
Download and run the autoresearch setup skill from
https://github.com/declankra/ai-engineering-public. Start by reading
skills/autoresearch/SKILL.md, then guide me through setting it up for my
project.
```

Already have a loop set up? Run it:

```text
Read skills/autoresearch/SKILL.md from
https://github.com/declankra/ai-engineering-public and run the autoresearch
loop defined in <path-to-your-program.md>.
```

Or install the skill globally so any session can invoke `/autoresearch`:

```bash
git clone https://github.com/declankra/ai-engineering-public.git
ln -s "$(pwd)/ai-engineering-public/skills/autoresearch" ~/.claude/skills/autoresearch
```

## Skill Anatomy

Each skill uses progressive disclosure:

```text
skills/<name>/
|-- SKILL.md       # trigger, workflow, judgment, proof, and gotchas
|-- templates/     # reusable output or operating surfaces
|-- references/    # deeper doctrine loaded only when needed
`-- evals/         # activation and behavior cases where testable
```

Agents should load `SKILL.md` first and pull in templates, references, and
evals only when the skill routes them there.

## What's Coming

More skills graduate here as they prove out in real deployments. Watch the
repo or follow along at [Kramper Engineering](https://kramperengineering.com).
