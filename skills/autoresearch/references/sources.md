# Source and scope

Inspired by Anthropic's official `claude-api` skill, reviewed at commit
[`8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4`](https://github.com/anthropics/skills/commit/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4),
and the existing production-derived autoresearch workflow in this repository.
This is an original principle-based distillation, not a vendored copy of the skill.
The upstream skill is [Apache-2.0 licensed](https://github.com/anthropics/skills/blob/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4/skills/claude-api/LICENSE.txt).

Read upstream to understand provenance, not as an additional execution contract:

- [Build an eval](https://github.com/anthropics/skills/blob/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4/skills/claude-api/shared/evals/build-eval.md): question-led measurement, representative cases, grader choice and a reviewable pilot.
- [Hillclimb](https://github.com/anthropics/skills/blob/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4/skills/claude-api/shared/evals/eval-hillclimb.md): bounded hypotheses, persistent evidence and keep/reject decisions.
- [Eval audit](https://github.com/anthropics/skills/blob/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4/skills/claude-api/shared/evals/eval-audit.md): inspect labels, execution and grading before trusting a score.
- [Cost hillclimb](https://github.com/anthropics/skills/blob/8a1541c4a3ffa5a20a5a91de0dcf3f0bab1d1ef4/skills/claude-api/shared/evals/cost-hillclimb.md): compare complete configurations and confirm the selected combination.

## Deliberate departures

The portable boundary is the evaluated behavior and its evidence. SDK calls,
provider model lists, effort levels, cache prices, reporting formats and agent UI
tools are resolved in the target environment. Existing authorization replaces
repeated ceremonial sign-offs; unresolved product judgment still requires examples
and an owner decision.

Data used repeatedly to select candidates is validation, regardless of its filename.
Final confirmation uses untouched data. Repeats and related cases are not treated
as independent population samples; compare paired differences with suitable
uncertainty. Retain end-to-end completion denominators alongside scored quality so
failure exclusions cannot manufacture a win. Cross-provider search assumes neither
comparable effort labels nor a monotonic model hierarchy.
