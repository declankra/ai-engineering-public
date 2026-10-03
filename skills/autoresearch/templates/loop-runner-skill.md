# Optional project entry point

Prefer the installed canonical skill. If a project must carry its own entry point,
use the skill location its agent runtime supports and point to one accessible
canonical package. The package includes `SKILL.md` and its linked references; a
copied entry file alone is incomplete. Do not duplicate the optimization procedure.

````markdown
---
name: autoresearch
description: Run this project's measured AI improvement loop. Use to continue the local program; do NOT use for unmeasured prompt edits.
---

Read `[accessible path to the canonical autoresearch/SKILL.md]` and follow its
hillclimb mode using `[path to this project's program.md]`. Resolve linked
references relative to that canonical package. The program owns the local runner,
metrics, mutable surface, authorization and resource limits.
````

Replace both paths with verified locations. For an independently distributed
project, ship the whole portable package with provenance and update ownership, or
depend on an installed package at a documented location. Avoid a wrapper pointing
to the author's private machine.
