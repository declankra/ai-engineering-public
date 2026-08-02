## PROJECT README

Create one per dataset/segment in `projects/[project-id]/`:

```markdown
# Project: [project-id]

## Review Status

- **Reviewer:** [who reviewed the gold labels]
- **Review date:** [when]
- **Status:** [e.g., "owner-reviewed", "partially-reviewed", "pending-review"]
- **Accepted entries:** [N] / [total]
- **Pending entries:** [N] (awaiting [what — e.g., stakeholder confirmation, domain expert review])

## Dataset

- [N] total entries across [N] segments/sources/categories
- [Breakdown by segment, e.g.:]
  - Segment A: [N] entries
  - Segment B: [N] entries
  - Segment C: [N] entries

## Source artifacts

[List the real input files this project benchmarks against, e.g.:]
- `path/to/source-document-1.pdf`
- `path/to/source-document-2.csv`
- `path/to/source-records.jsonl`

## Gold label file

`[filename — e.g., extraction-map.json, labels.json, ground-truth.jsonl]`

### Schema

Each entry contains:

**Scored fields** (used in metric computation):
- [list each field and what it represents]

**Source values** (human reference, not scored):
- [list each field — these help reviewers but don't affect the score]

**Review metadata:**
- `reviewStatus` — `accepted` or `pending`
- `reviewNotes` — array of notes explaining label decisions

### Review notes

[Domain-specific notes about label decisions, ambiguities resolved, stakeholder clarifications. This section preserves the domain knowledge that informed the labels.]

[e.g., "Resolved 2026-03-24: reviewer confirmed that two identifiers are acceptable aliases for this entity" or "When the source reports a grouped value, expectedOutput.normalizedValue is converted to the single-unit basis"]
```

---
