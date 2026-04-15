---
title: "What's New in DataJoint 2.2"
type: source
tags: [changelog, release-notes]
date: 2026-04-15
source_file: raw/whats-new-22.md
---

## Summary

DataJoint 2.2 introduces `dj.Instance` for isolated multi-tenant connections, thread-safe mode (`DJ_THREAD_SAFE=true`), and graph-driven diagram operations replacing error-driven cascade. `Diagram.cascade()`, `Diagram.restrict()`, and `Diagram.prune()` provide programmatic ERD manipulation. `database.name` added (new) and `database.database_prefix` deprecated.

## Key Changes

- **`dj.Instance`**: Isolated Config+Connection pair; `inst.Schema()`, `inst.FreeTable()`, **kwargs for config overrides
- **Thread-safe mode**: `DJ_THREAD_SAFE=true` disables global `dj.config` and `dj.conn()`; only `dj.Instance` works
- **Graph-driven diagrams**: `Diagram.cascade(table)` (propagate downstream), `Diagram.restrict(table)` (prune non-ancestors), `Diagram.prune(table)` (remove subtree)
- **Propagation rules**: direct copy (FK to exact PK → same mapping), aliased projection (FK to projected subset → remap), full projection (remaining FKs → full PK mapping)
- **`database.name`**: New setting for PostgreSQL `dbname` parameter
- **`database.database_prefix`**: Deprecated in 2.2.1 — use `database.name`

## Key Quotes

> "`dj.Instance` solves the global state problem. Every web request, every test, every worker gets its own isolated connection — no accidental cross-contamination."

## Connections

- [[dj.Instance]] — the new Instance API
- [[configuration]] — thread-safe mode and new settings
- [[use-instances]] — practical guide to dj.Instance
- [[delete-data]] — Diagram.cascade() used for deletion preview

## Contradictions

None identified.
