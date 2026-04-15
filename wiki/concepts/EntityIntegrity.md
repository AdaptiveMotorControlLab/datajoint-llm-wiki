---
title: "Entity Integrity"
type: concept
tags: [core-concept, schema-design, integrity]
sources: [entity-integrity, design-primary-keys, normalization, define-tables]
last_updated: 2026-04-15
---

# Entity Integrity

Entity integrity in DataJoint ensures that primary keys uniquely and permanently identify real-world entities, and that the data model accurately reflects the structure of the scientific domain being modeled.

## The Three Questions

For every table, ask:
1. **What entity does this row represent?** — Rows represent instances of a real-world entity (mouse, session, neuron)
2. **What attributes uniquely identify it?** — Use natural, meaningful identifiers
3. **Can those attributes ever change?** — Primary keys must be immutable

## Dimensions

A "dimension" is an independent aspect of entity identity. Each new independent variable in the primary key adds a dimension:

- `Subject`: 1D (subject_id)
- `Session`: 2D (subject_id, session_idx)  
- `Trial`: 3D (subject_id, session_idx, trial_idx)

## Dimension Rules

| Table Tier | Can Add Dimensions? |
|------------|---------------------|
| Manual | Yes — entry points introduce real-world entities |
| Lookup | Yes — parameter sets are their own dimension |
| Imported | No — inherits all PK from upstream (but can introduce if there's no FK-only PK) |
| Computed | **No** — must inherit all PK attributes from foreign keys |
| Part | **Yes** — can introduce new dimensions (e.g., Cell in Segmentation.Cell) |

## Attribute Lineage

Every attribute in the system has a "lineage" — the original table that introduced it. Lineage propagates through foreign key chains. DataJoint's [[SemanticMatching]] uses lineage to validate joins.

## Connections

- [[PrimaryKeys]] — practical PK design guidance
- [[SemanticMatching]] — attribute lineage for join safety
- [[TableTiers]] — tier rules for dimension introduction
- [[Normalization]] — intrinsic attributes principle
