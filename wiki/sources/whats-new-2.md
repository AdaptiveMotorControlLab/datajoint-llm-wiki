---
title: "What's New in DataJoint 2.0"
type: source
tags: [changelog, release-notes, major-release]
date: 2026-04-15
source_file: raw/whats-new-2.md
---

## Summary

DataJoint 2.0 is a major release introducing: Object-Augmented Schema (OAS) for unified relational+object storage, explicit type system (replacing implicit `longblob` with `<blob>`, `<npy@>`, etc.), Jobs 2.0 redesign (per-table `~~table_name` tables replacing `~jobs`), semantic matching for safe joins, and a new unified configuration system. Migration requires 4 phases.

## Key Changes

- **OAS**: External stores now part of schema; hash-addressed and schema-addressed storage modes
- **Explicit types**: `longblob` → `<blob>` (in-DB), `longblob` + external store → `<blob@>` 
- **Jobs 2.0**: Each computed table gets its own `~~table_name` jobs table; per-table job coordination
- **Semantic matching**: Joins now validate attribute lineage — same name but different origin raises error instead of silently joining wrong data
- **Unified config**: Single `stores` config replaces separate blob and external store configs
- **ObjectRef API**: `ref.open()`, `ref.download()` for external object access

## Key Quotes

> "DataJoint 2.0 makes the relationship between your pipeline's relational structure and its large object storage first-class — the schema knows about both."

## Connections

- [[migrate-to-v20]] — migration guide
- [[ObjectStorage]] — the OAS system introduced here
- [[SemanticMatching]] — new join semantics
- [[JobsSystem]] — Jobs 2.0 redesign
- [[TypeSystem]] — explicit type system

## Contradictions

None identified.
