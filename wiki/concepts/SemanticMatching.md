---
title: "Semantic Matching"
type: concept
tags: [core-concept, joins, query-algebra]
sources: [semantic-matching, operators, faq, migrate-to-v20, entity-integrity]
last_updated: 2026-04-15
---

# Semantic Matching

Semantic matching is DataJoint's join validation system that prevents accidental joins on unrelated attributes that happen to share a name. Introduced in DataJoint 2.0.

## The Problem

SQL's `NATURAL JOIN` joins on any column with the same name. Two unrelated tables may both have a column called `name`, `id`, or `type` — a natural join silently produces wrong results.

## DataJoint's Solution

DataJoint joins only on **homologous** attributes: attributes with both:
1. The **same name**
2. The **same lineage** (traced through foreign key declarations)

```python
# CORRECT: subject_id has same lineage in both tables (Session references Subject)
Subject * Session  # joins on subject_id — safe

# ERROR: both tables have 'name' but from different origins
Student * Course   # NonHomologousError — protects you from wrong join
```

## Lineage Tracking

Lineage is established at table definition:
- A table that declares `subject_id : varchar(20)` is the **origin** — it introduces the lineage
- A table that declares `-> Subject` **inherits** the lineage of all Subject's PK attributes
- Lineage propagates transitively through FK chains

## Resolution

When a conflict occurs (same name, different lineage), rename with `.proj()`:
```python
# Rename to make origins clear
Student * Course.proj(..., course_name='name')
```

## Impact on Migration

Semantic matching was introduced in DataJoint 2.0. Existing code with joins on same-named attributes from different origins that previously "worked" (by accident) will now raise errors. See [[migrate-to-v20]] for migration guidance.

## Connections

- [[QueryAlgebra]] — semantic matching is part of the join operator definition
- [[operators]] — join (*) and restriction (&) both use semantic matching
- [[EntityIntegrity]] — lineage as part of integrity
- [[PrimaryKeys]] — PK lineage is the source of all lineage chains
