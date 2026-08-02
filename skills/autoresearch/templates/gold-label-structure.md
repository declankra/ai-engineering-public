## GOLD LABEL STRUCTURE

Gold label files should follow these principles:

### 1. Separate scorer inputs from reviewer context

The fields the benchmark scores against (e.g., `expectedOutput`) must be distinct from fields that exist for human review (e.g., `sourceValues`, `reviewNotes`). This prevents the scorer from accidentally using human-only context and keeps the benchmark honest.

### 2. Include `reviewStatus` per entry

Use `accepted` for fully verified labels, `pending` for labels awaiting confirmation. This enables segmented scoring — optimize against the settled slice first, track the full set as a shadow diagnostic.

Why this matters: if you mix settled and unsettled labels, you can't tell if a score drop is because the model got worse or because the label was wrong. Segment first, optimize on the clean slice.

### 3. Anchor to source artifacts

Each label should reference what it was derived from:
- Source file (which PDF, document, query)
- Location within the source (page number, line reference, section)
- Human-readable context excerpt (`sourceContext`)

This makes labels auditable. When a mismatch shows up, you can trace it back to the source and determine whether the label or the model is wrong.

### 4. Preserve reviewer reasoning

`reviewNotes` capture WHY a label is what it is — especially for ambiguous cases. This is domain knowledge that helps future reviewers and the agent understand edge cases.

Good review notes:
- "The short label and long label are both acceptable aliases for this entity — confirmed with reviewer"
- "Source reports a grouped value — normalized to single-unit basis using the documented policy"
- "Two acceptable identifiers exist: the source-local ID and the external reference ID"

### Example structure

Adapt field names to your domain:

```json
{
  "schemaVersion": 1,
  "projectId": "[project-id]",
  "status": "[review-status]",
  "createdAt": "[date]",
  "reviewedAt": "[date]",
  "sourceArtifacts": {
    "[artifact-type]": "[path-or-reference]",
    "[artifact-type]": "[path-or-reference]"
  },
  "[policy-notes]": "[Any domain-specific scoring policies, e.g., 'When the source reports a grouped value, expectedOutput.normalizedValue is converted to single-unit basis']",
  "entries": [
    {
      "entryId": "[unique-id]",
      "reviewStatus": "accepted",
      "sourceRef": "[where in the source artifact — e.g., page 1, item 100]",
      "sourceContext": "[human-readable excerpt from the source — e.g., 'Row 100 · Qty 60000 · ID 744110647 · grouped value 66.35 per 1000']",
      "sourceValues": {
        "[raw-field-1]": "[value as it appears in the source]",
        "[raw-field-2]": "[value as it appears in the source]",
        "[acceptable-alternatives]": ["[alt-1]", "[alt-2]"]
      },
      "expectedOutput": {
        "[scored-field-1]": "[correct value for scoring]",
        "[scored-field-2]": "[correct value for scoring]"
      },
      "seedReferences": [
        {
          "[cross-ref-field-1]": "[value from another data source for comparison]"
        }
      ],
      "reviewNotes": [
        "[why this label is correct]",
        "[any ambiguities and how they were resolved]",
        "[stakeholder confirmation with date if applicable]"
      ]
    }
  ]
}
```

### Build a review surface if the data is complex

If reviewing raw label files is painful (nested JSON, many fields, cross-referenced values), build a visual tool before investing more time in labels. Options:

- **HTML preview** — single-file viewer that renders entries with source context side by side
- **Browser-based dataset builder** — interactive tool to create, edit, validate, and export labels in the benchmark's exact format
- **Structured diff view** — expected vs actual per entry, color-coded

The easier it is to review labels, the better the labels get. If you're doing something manually and repeatedly, that's a trigger to automate it. Good baseline data is the foundation — make getting it right easy.

### What makes a good gold label file

- Every entry is traceable to a source artifact
- Scored fields are cleanly separated from context fields
- Review status lets you score on settled vs. unsettled subsets
- Reviewer notes preserve domain knowledge for future reference
- Policy notes explain any normalization or transformation rules
- Alternative acceptable values are documented (e.g., aliases, source-local IDs, external reference IDs)
- **Reviewable at a glance** — if a human can't comfortably inspect labels, build a review surface

```
┌─────────────────────────────────────────────────┐
│ ⚠ LESSON FROM THE FIELD                        │
│                                                 │
│ The gold label file is the most important file  │
│ in the entire setup. If it's wrong, the loop    │
│ optimizes toward wrong. Spend real time on it.  │
│                                                 │
│ Separate what gets scored from what's there     │
│ for human context. Include reviewStatus so you  │
│ can score against the settled subset. Anchor    │
│ every label to its source. And preserve the     │
│ reasoning — future you will forget why that     │
│ edge case was labeled that way.                 │
│                                                 │
│ — autoresearch in production                    │
└─────────────────────────────────────────────────┘
```
