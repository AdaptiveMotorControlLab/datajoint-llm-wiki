---
title: "Primary Keys"
type: concept
tags: [core-concept, schema-design, integrity]
sources: [design-primary-keys, entity-integrity, definition-syntax, normalization]
last_updated: 2026-04-15
---

# Primary Keys

Primary keys in DataJoint identify entities uniquely and permanently. DataJoint strongly prefers **natural keys** (meaningful real-world identifiers) over surrogate keys (auto-increment IDs). The primary key is declared above the `---` separator in the table definition.

## Design Principles

1. **What entity does this row represent?** — Each row represents one instance of a real-world entity
2. **What attributes uniquely identify it?** — Use natural identifiers (animal ID, session date, trial number)
3. **Can those attributes ever change?** — Primary keys must be immutable after insertion

## Natural vs Surrogate Keys

**Natural key** (preferred):
```python
subject_id : varchar(20)  # "M001", "Rat42" — meaningful
session_date : date        # "2024-01-15" — stable
```

**Surrogate key** (avoid):
```python
id : int32  # auto_increment — no semantic meaning
```

Why avoid `AUTO_INCREMENT`:
1. No semantic meaning (can't tell what the row represents)
2. Requires extra JOINs to get meaningful data
3. Breaks distributed insertion (ordering conflicts)
4. Hides data structure from query algebra
5. Conflicts with DataJoint's dimension tracking

## Composite Keys and Dimensions

Each independent aspect of entity identity is a "dimension":
- `subject_id` + `session_idx` = 2-dimensional key (animal × session)
- Adding `trial_idx` = 3-dimensional (animal × session × trial)

**Rule**: Computed tables inherit all their PK attributes through foreign keys — they cannot add new dimensions. Only Part tables may introduce new dimensions.

## Foreign Keys in Primary Keys

```python
# Session inherits subject_id from Subject, adds session_idx
-> Subject       # inherits subject_id lineage
session_idx : int16  # new dimension at this level
```

## Connections

- [[EntityIntegrity]] — the three questions framework
- [[design-primary-keys]] — practical how-to guide
- [[definition-syntax]] — PK declaration syntax
- [[SemanticMatching]] — PK lineage used in join validation
