---
title: "Define Tables"
type: source
tags: [how-to, schema-definition, tables]
date: 2026-04-15
source_file: raw/define-tables.md
---

## Summary

How to define DataJoint tables using the `@schema` decorator and class-based definition syntax. Tables are defined by class inheriting from `dj.Manual`, `dj.Lookup`, `dj.Imported`, or `dj.Computed`. The `definition` string declares primary key attributes (above `---`), secondary attributes (below `---`), foreign keys (`->`), defaults, and modifiers.

## Key Claims

- `@schema` decorator registers the class with a database schema and creates the table if it doesn't exist
- Primary key attributes are declared before `---`; secondary attributes after
- Foreign keys use `-> ParentTable` syntax and inherit all parent primary key attributes
- `[nullable]` modifier makes FK nullable (secondary section only); `[unique]` adds UNIQUE INDEX
- Part tables are inner classes of master tables: `class Part(dj.Part): ...`
- Lookup tables should have `contents = [...]` for auto-population
- Indexes are declared in the secondary section: `INDEX (attr1, attr2)` or `UNIQUE INDEX (attr)`

## Key Quotes

> "The `---` separator is mandatory. Everything above is the primary key; everything below is secondary data."

## Connections

- [[DefinitionSyntax]] — formal grammar reference for the definition language
- [[TableTiers]] — the four tier classes (Manual, Lookup, Imported, Computed)
- [[PrimaryKeys]] — primary key design principles
- [[Schema]] — the schema object that registers tables

## Contradictions

None identified.
