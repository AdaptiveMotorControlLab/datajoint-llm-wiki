---
title: "Overview"
type: synthesis
tags: [datajoint, overview, synthesis]
sources: [all]
last_updated: 2026-04-15
---

# DataJoint LLM Wiki — Overview

*This page is maintained by the LLM. It is updated on every ingest to reflect the current synthesis across all sources.*

---

## What Is DataJoint?

DataJoint is an open-source Python (and MATLAB) framework for building **scientific data pipelines** backed by MySQL or PostgreSQL. It unifies three paradigms into a single coherent system:

1. **Relational model** (Codd): a complete query algebra with algebraic closure
2. **Entity-relationship model** (Chen): typed tables with semantic tier classification
3. **Workflow automation**: automatic computation via `populate()` / `make()`

The result is a system where the schema IS the workflow — if a row doesn't exist in a Computed table, the computation hasn't happened yet. See [[RelationalWorkflowModel]].

---

## Core Concepts at a Glance

### Table Tiers

Every DataJoint table inherits from one of five tier classes that determine who populates it:

| Tier | Class | Populated By |
|------|-------|-------------|
| Manual | `dj.Manual` | Human researchers via `insert()` |
| Lookup | `dj.Lookup` | Code (static `contents` list) |
| Imported | `dj.Imported` | `make()` from external files |
| Computed | `dj.Computed` | `make()` from upstream tables |
| Part | `dj.Part` (inner class) | Master's `make()` only |

See [[TableTiers]] for detailed behavior.

### Query Algebra

Seven composable operators on entity sets:
- `A & condition` — restriction (filter)
- `A - condition` — anti-restriction (exclude)
- `A.proj(...)` — projection (select/rename/compute)
- `A * B` — join (on homologous attributes)
- `A.extend(B)` — extension (add optional attributes)
- `A.aggr(B, ...)` — aggregation (group and summarize)
- `A + B` — union (combine entity sets)

All operators return entity sets → queries are composable. See [[QueryAlgebra]].

### Computation Model

```python
@schema
class MyComputed(dj.Computed):
    definition = "-> Upstream\n---\nresult : float64"
    
    def make(self, key):
        data = (Upstream & key).fetch1()
        self.insert1({**key, 'result': compute(data)})

MyComputed.populate()  # processes all missing keys
```

`populate()` finds un-processed keys, calls `make(key)` once per key. Jobs 2.0 (`~~table_name`) tracks state. See [[ComputationModel]].

### Object-Augmented Schema (OAS)

DataJoint 2.0 introduced the OAS — large objects are first-class schema citizens:

- **Inline** (`<blob>`, `<attach>`): stored in the database
- **Hash-addressed** (`<blob@>`, `<attach@>`): content-deduplicating external storage
- **Schema-addressed** (`<npy@>`, `<object@>`): human-browsable key-based paths
- **Referenced** (`<filepath@>`): user-managed file references

See [[ObjectStorage]].

### Semantic Matching

DataJoint joins only on **homologous** attributes — same name AND same foreign key lineage. This prevents accidental joins on unrelated columns sharing a name. Introduced in DataJoint 2.0. See [[SemanticMatching]].

---

## Version History

| Version | Key Features |
|---------|-------------|
| 2.0 | OAS, explicit type system, Jobs 2.0, semantic matching, unified config |
| 2.1 | PostgreSQL support, diagram enhancements, singleton tables |
| 2.2 | `dj.Instance`, thread-safe mode, graph-driven diagram operations |
| 2.2.1 | `database.name` added, `database.database_prefix` deprecated |

---

## Key Design Principles

1. **Natural keys**: Primary keys should be meaningful real-world identifiers, not auto-increment IDs
2. **Workflow normalization**: One table = one workflow step
3. **Intrinsic attributes**: Secondary attributes must depend only on the full primary key
4. **Computed tables add no dimensions**: only Part tables may introduce new primary key dimensions
5. **Real databases in tests**: Never mock the database — SQL semantics matter
6. **One schema per module**: organize pipeline code as one Python module per DataJoint schema

---

## People and Organizations

- Dimitri Yatsenko — creator, Baylor College of Medicine (2009), now DataJoint Inc.
- DataJoint Inc. — formerly Vathes LLC (2016), incorporated 2024, $4.9M seed 2025

---

## How to Use This Wiki

- **Explain a concept**: Query the concept pages — [[TableTiers]], [[QueryAlgebra]], [[ComputationModel]]
- **Solve a specific task**: Check how-to sources — [[run-computations]], [[handle-errors]], [[configure-storage]]
- **Look up API**: Check reference sources — [[operators]], [[configuration]], [[errors]], [[fetch-results]]
- **Understand design decisions**: [[faq]], [[relational-workflow-model]], [[entity-integrity]]
- **Migrate or upgrade**: [[migrate-to-v20]], [[whats-new-2]], [[whats-new-21]], [[whats-new-22]]
