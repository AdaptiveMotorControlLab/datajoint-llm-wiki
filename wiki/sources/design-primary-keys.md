---
title: "Design Primary Keys"
type: source
tags: [how-to, primary-keys, schema-design]
date: 2026-04-15
source_file: raw/design-primary-keys.md
---

## Summary

Guide for designing primary keys in DataJoint. Natural keys (meaningful identifiers like `subject_id`, `session_date`) are strongly preferred over surrogate keys (auto-increment IDs). Composite keys encode entity dimensions. Primary keys should be stable, meaningful, and not include attributes that can change. Foreign keys in the primary key inherit parent dimensions.

## Key Claims

- Natural keys are strongly preferred: `mouse_id`, `session_date` rather than auto-increment `id`
- Five reasons to avoid `AUTO_INCREMENT`: no semantic meaning, requires extra JOIN to find what you want, breaks distributed insertion, hides data structure, conflicts with DataJoint's query algebra
- Composite keys encode multiple independent dimensions (e.g., `subject_id + session_idx`)
- A primary key should uniquely identify an entity and never change after insertion
- UUIDs are acceptable as natural keys when data originates from external systems
- Foreign keys in the primary key inherit the parent's dimensions into the child

## Key Quotes

> "If your primary key has no meaning outside the database, it's a surrogate key and probably wrong for DataJoint. The key should tell you what the row represents."

## Connections

- [[PrimaryKeys]] — concept page for primary key theory
- [[EntityIntegrity]] — how PK design ensures correctness
- [[definition-syntax]] — syntax for declaring PKs

## Contradictions

None identified.
