---
title: "Entity Integrity"
type: source
tags: [conceptual, primary-keys, schema-design, integrity]
date: 2026-04-15
source_file: raw/entity-integrity.md
---

## Summary

Entity integrity in DataJoint means primary keys must uniquely and permanently identify real-world entities. Three questions guide PK design: What entity does this row represent? What attributes uniquely identify it? Will those attributes ever change? Natural keys are preferred. Computed tables must not introduce new dimensions — only Part tables may do so.

## Key Claims

- Three questions for PK design: (1) What entity? (2) What identifies it? (3) Can identifiers change?
- "Dimensions" are independent aspects of entity identity (e.g., subject + session + trial = 3 dimensions)
- Computed tables must inherit all their PK attributes from foreign keys — no new dimensions allowed
- Part tables CAN introduce new dimensions (e.g., Segmentation.Cell introduces `cell_id`)
- Attribute lineage tracks which table originally introduced each attribute through the FK graph
- Natural vs surrogate: natural keys have real-world meaning; surrogate keys (auto-increment) do not
- Composite keys encode multiple dimensions explicitly

## Key Quotes

> "Computed tables never introduce new dimensions — they only compute properties of entities that already exist upstream. If you need a new dimension in a computed table, use a Part table."

> "A primary key that can change is not a primary key — it's a bug."

## Connections

- [[PrimaryKeys]] — concept page synthesizing PK design across sources
- [[design-primary-keys]] — practical how-to guide
- [[TableTiers]] — how Part tables differ from Computed tables
- [[SemanticMatching]] — attribute lineage used in join validation

## Contradictions

None identified.
