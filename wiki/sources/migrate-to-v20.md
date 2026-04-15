---
title: "Migrate to DataJoint 2.0"
type: source
tags: [how-to, migration, versioning]
date: 2026-04-15
source_file: raw/migrate-to-v20.md
---

## Summary

Four-phase migration guide for upgrading to DataJoint 2.0. Phase 1: Branch and update code (explicit type syntax, unified stores config). Phase 2: Test 2.0 compatibility against existing data. Phase 3: Migrate existing blob data (add `:type:` comment prefix to blob columns). Phase 4: Enable new 2.0 features (Jobs 2.0, OAS, semantic matching). AI migration agent prompts provided.

## Key Claims

- DataJoint 2.0 introduces breaking changes: explicit `<blob>` type syntax replaces implicit `longblob`, unified stores config replaces separate stores
- Phase 3 data migration: add `:type:` prefix to blob column comments to register type with OAS
- API changes table covers all renamed/removed methods
- AI migration agent prompts help automate code changes (provided in the doc)
- Jobs 2.0 requires dropping and recreating the old `~jobs` table
- Semantic matching may break existing joins on attributes with the same name but different lineage

## Key Quotes

> "Don't skip Phase 2 — test 2.0 against a copy of your production data before migrating. Some joins may silently break due to semantic matching."

## Connections

- [[whats-new-2]] — what changed in 2.0
- [[SemanticMatching]] — the new join validation system that may affect existing code
- [[ComputationModel]] — Jobs 2.0 changes
- [[TypeSystem]] — new explicit type system

## Contradictions

None identified.
