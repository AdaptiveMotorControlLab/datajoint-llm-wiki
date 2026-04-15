---
title: "Normalization"
type: source
tags: [conceptual, schema-design, normalization]
date: 2026-04-15
source_file: raw/normalization.md
---

## Summary

DataJoint extends relational normalization with two workflow-specific principles. The Workflow Normalization Principle: each table represents one step in the data workflow, not just any grouping of attributes. The Intrinsic Attributes Principle: a table's secondary attributes must depend only on its primary key — not on partial keys or transitive dependencies. Temporal tables handle relationships that change over time.

## Key Claims

- Workflow normalization: one table = one workflow step (one source, one computation, one result set)
- Intrinsic attributes principle: all secondary attributes must be functions of the full primary key only (≈ BCNF)
- Example violation: mouse housing in the Session table — housing can change between sessions, so it belongs in a separate Housing table with temporal keys
- Temporal tables: add `date_effective : date` to the primary key for relationships that change over time
- The "mouse housing" example shows why splitting tables is often required even when SQL allows it

## Key Quotes

> "The workflow normalization principle is DataJoint's extension of Boyce-Codd Normal Form: each table represents exactly one step in the scientific workflow."

> "If a secondary attribute depends on only part of the primary key, the table is not normalized — split it."

## Connections

- [[EntityIntegrity]] — primary key correctness
- [[RelationalWorkflowModel]] — the theoretical foundation including workflow normalization
- [[TableTiers]] — table tiers as workflow steps
- [[design-primary-keys]] — practical PK design

## Contradictions

None identified.
