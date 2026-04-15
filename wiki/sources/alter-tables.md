---
title: "Alter Tables"
type: source
tags: [how-to, schema-management, migration]
date: 2026-04-15
source_file: raw/alter-tables.md
---

## Summary

`MyTable.alter()` applies non-destructive schema changes in-place using MySQL's `ALTER TABLE`. Supported changes include adding new secondary attributes (with defaults or as nullable), dropping secondary attributes, modifying attribute types/defaults, and renaming secondary attributes. Primary keys, foreign keys, and indexes cannot be changed with `alter()`.

## Key Claims

- `alter()` is idempotent — safe to call repeatedly; only makes changes when the live definition differs from the class definition
- Cannot alter primary key attributes, foreign key declarations, or index definitions
- To add job metadata columns (after enabling `jobs.add_job_metadata`), call `table.alter()` explicitly on all computed tables
- Unsupported changes require creating a new table, migrating data, and dropping the old one
- `alter(prompt=True)` (default) requires interactive confirmation; `alter(prompt=False)` skips it

## Key Quotes

> "alter() is safe to call even if no changes are needed — it is a no-op if the live schema matches the class definition."

## Connections

- [[Schema]] — `alter()` is a method on table objects managed by Schema
- [[ComputationModel]] — `add_job_metadata` columns added via `alter()` after enabling job metadata
- [[DefinitionSyntax]] — new attribute syntax for additions/modifications

## Contradictions

None identified.
