---
title: "Delete Data"
type: source
tags: [how-to, data-management, deletion]
date: 2026-04-15
source_file: raw/delete-data.md
---

## Summary

DataJoint deletion cascades automatically through foreign key dependencies. `table.delete()` prompts for confirmation (safemode), deletes matching rows and all dependent downstream rows. `delete_quick()` skips confirmation. Part table integrity can be enforced (delete master if part exists). New in 2.2: `Diagram.cascade()` for graph-driven cascade preview before deletion.

## Key Claims

- `(Table & condition).delete()` deletes matching rows and cascades to all downstream dependents
- `safemode=True` (default) shows cascade plan and prompts; `safemode=False` or `delete_quick()` skips prompt
- `part_integrity=True` raises error if attempting to delete master row that has part rows
- Deleting a parent row deletes all child rows (FK cascade) — this includes computed results
- To recompute: delete downstream rows; populate() will fill them again
- `Diagram.cascade(table & condition)` (new in 2.2) previews the cascade subgraph before deletion

## Key Quotes

> "Deleting a row from an upstream table propagates through the entire downstream pipeline — use safemode to review the cascade before committing."

## Connections

- [[Schema]] — deletion respects schema boundaries
- [[ComputationModel]] — deleting computed rows triggers recomputation on next populate()
- [[whats-new-22]] — Diagram.cascade() introduced in 2.2

## Contradictions

None identified.
