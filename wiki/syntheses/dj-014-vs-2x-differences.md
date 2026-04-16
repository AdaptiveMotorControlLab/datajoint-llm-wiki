---
title: "Differences Between DataJoint 0.14 and 2.x"
type: synthesis
tags: [migration, versioning, changelog, breaking-changes]
sources: [whats-new-2, whats-new-21, whats-new-22, migrate-to-v20, versioning]
last_updated: 2026-04-16
---

## Differences Between DataJoint 0.14 and 2.x

> **Note:** The wiki's baseline is DataJoint 2.0 — no pre-2.0 documentation is included. What
> follows is synthesised from what the 2.0 migration guide and changelogs describe as changed or
> replaced relative to the old API.

---

### Type System — the biggest surface-area change

In 0.14, large array data was stored implicitly as `longblob` with no schema-level type
information. DataJoint 2.0 replaced this with an explicit type system ([[TypeSystem]]):

| 0.14 | 2.x |
|------|-----|
| `my_array : longblob` | `my_array : <blob>` (in-DB) |
| `longblob` + external store config | `my_array : <npy@store>` (object storage) |

The `<type>` syntax makes the codec and storage location part of the schema definition.
Migration requires adding `:type:` prefix comments to existing blob columns (Phase 3 of
[[migrate-to-v20]]).

---

### Object Storage — from bolted-on to first-class schema citizen

0.14 had a separate, loosely integrated external store system with its own config block.
DataJoint 2.0 introduced **Object-Augmented Schema (OAS)** ([[ObjectStorage]]):

- External stores are now defined in a unified `stores` config (replacing the old separate
  blob and external store configs)
- Hash-addressed and schema-addressed storage modes
- `ObjectRef` API: `ref.open()`, `ref.download()` for accessing stored objects
- `staged_insert1()` for atomic write-then-register patterns

---

### Jobs System — global table replaced by per-table tables

0.14 used a single global `~jobs` table shared across all computed tables. DataJoint 2.0
introduced **Jobs 2.0** ([[JobsSystem]]):

| 0.14 | 2.x |
|------|-----|
| Single `~jobs` table | Per-table `~~table_name` tables |
| Shared job queue | Isolated per-table queues |
| Limited error tracking | Full traceback stored in `error_stack` |

Migration requires dropping the old `~jobs` table; new per-table tables are auto-created on
first `populate()`.

---

### Semantic Matching — joins now validated

In 0.14, joining two tables on attributes with the same name always succeeded, even if those
attributes came from different lineages (a silent correctness hazard). DataJoint 2.0
[[SemanticMatching]] raises an error instead:

```python
# 0.14: silently joined — possibly wrong
TableA * TableB   # both have 'session_id' but from different FKs → no error

# 2.x: raises SemanticMatchingError if lineage differs
TableA * TableB   # → error: 'session_id' has different origin in each table
```

Per [[migrate-to-v20]]: *"Some joins may silently break due to semantic matching"* — the
migration guide specifically warns to test 2.0 against a copy of production data before switching.

---

### New in 2.1

- **PostgreSQL 15+ support** — `database.backend = "postgresql"`. 0.14 was MySQL-only.
- **Singleton tables** — tables with no primary key (single-row config/state tables) now supported
- **Diagram enhancements** — Mermaid output, LR/TB direction, schema grouping, collapse

See [[whats-new-21]].

---

### New in 2.2

- **`dj.Instance`** — isolated Config+Connection pairs for multi-tenant use (web apps, parallel
  tests). 0.14 used global `dj.config` and `dj.conn()` everywhere.
- **Thread-safe mode** — `DJ_THREAD_SAFE=true` disables global state; mandatory use of
  `dj.Instance`
- **Graph-driven diagram operations** — `Diagram.cascade()`, `.restrict()`, `.prune()`
- `database.database_prefix` deprecated in 2.2.1 in favour of `database.name`

See [[whats-new-22]].

---

### Breaking Changes Summary

| Area | What breaks |
|------|------------|
| Type syntax | All `longblob` definitions need rewriting to `<blob>` |
| Stores config | Old separate blob/external store config blocks removed |
| Jobs table | `~jobs` must be dropped; per-table tables take over |
| Semantic matching | Existing joins on same-name attributes from different FKs may raise errors |
| API renames | Various method renames — [[migrate-to-v20]] provides a full table |

---

## Sources

- [[whats-new-2]] — 2.0 changelog: OAS, explicit types, Jobs 2.0, semantic matching
- [[whats-new-21]] — 2.1: PostgreSQL, singleton tables, diagram enhancements
- [[whats-new-22]] — 2.2: dj.Instance, thread-safe mode, graph-driven diagrams
- [[migrate-to-v20]] — 4-phase migration guide, breaking changes list
- [[versioning]] — version history table and documentation baseline policy
