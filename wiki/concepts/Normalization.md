---
title: "Normalization"
type: concept
tags: [core-concept, schema-design, workflow]
sources: [normalization, relational-workflow-model, entity-integrity]
last_updated: 2026-04-15
---

# Normalization

DataJoint extends classical relational normalization (BCNF) with two workflow-specific principles that ensure the data model accurately represents the scientific workflow.

## Classical Normalization (BCNF)

Every non-key attribute must depend on the **whole** primary key:
- No partial dependencies (attr depends on subset of composite PK → split)
- No transitive dependencies (attr depends on another non-key attr → split)

## Workflow Normalization Principle

Each DataJoint table represents exactly **one step** in the scientific workflow:
- One data source (one file format, one instrument, one computation)
- One result type
- One set of entities at the same level of the dependency hierarchy

**Violation example**: Mixing session metadata with housing data in the same table
→ Housing can change between sessions but the session table has no mechanism to represent that

**Fix**: Separate Housing table with temporal key (add `date_effective : date` to PK)

## Intrinsic Attributes Principle

All secondary attributes in a table must be **intrinsic** to the primary key entity — they must be properties of exactly that entity, not a generalization or specialization:
- **Intrinsic**: session duration is a property of the session
- **Not intrinsic**: mouse species is a property of the mouse, not the session

## Common Normalization Mistakes

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Housing in Session | Housing can change → data duplication | Separate Housing table |
| Experiment params in Result | Params vary across runs → can't track | Separate Params Lookup table |
| Subject in Session attrs | Redundant (subject_id is in PK) | Remove, use join |

## Connections

- [[EntityIntegrity]] — primary key correctness (prerequisite)
- [[RelationalWorkflowModel]] — the theoretical framework
- [[TableTiers]] — one tier per workflow step role
- [[normalization]] — source page with examples
