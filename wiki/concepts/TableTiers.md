---
title: "Table Tiers"
type: concept
tags: [core-concept, schema-design, computation]
sources: [define-tables, computation-model, concepts-table-tiers-diagram, relational-workflow-model, entity-integrity]
last_updated: 2026-04-15
---

# Table Tiers

DataJoint classifies every table into one of five tiers based on **who or what populates it**. The tier is declared by inheriting from the appropriate base class. Tier determines color in ERD visualization and whether `populate()` is available.

## The Five Tiers

| Tier | Class | Who Populates | ERD Color |
|------|-------|---------------|-----------|
| Manual | `dj.Manual` | Humans via `insert()` | Green |
| Lookup | `dj.Lookup` | Code (static `contents`) | Yellow |
| Imported | `dj.Imported` | `make()` from external files | Blue |
| Computed | `dj.Computed` | `make()` from upstream tables | Red |
| Part | `dj.Part` (inner class) | Master's `make()` | Inherits master |

## Key Principles

- Tiers are not just labels — they carry execution semantics
- `Imported` and `Computed` implement `AutoPopulate` and must define `make(key)`
- `Lookup` tables should define `contents = [...]` for auto-population on schema creation
- `Part` tables are always inner classes of their master table; only the master's `make()` inserts into parts
- Computed tables must not introduce new primary key dimensions (only Part tables may)

## Connections

- [[ComputationModel]] — how Imported/Computed are populated
- [[EntityIntegrity]] — computed tables can't add new dimensions; Part tables can
- [[concepts-table-tiers-diagram]] — visual pipeline example with all tiers
