---
title: "Versioning Policy"
type: source
tags: [reference, versioning, documentation]
date: 2026-04-15
source_file: raw/versioning.md
---

## Summary

DataJoint documentation versioning policy. The 2.0 release is the baseline — documentation covers 2.0 and later. Features introduced after 2.0 are annotated with `version-added`, `version-changed`, or `version-deprecated` admonitions. Version history table maps releases to major features.

## Key Claims

- 2.0 is the documentation baseline — no documentation for pre-2.0 versions
- Admonitions mark version-specific content: `version-added`, `version-changed`, `version-deprecated`
- Version history: 2.0 (OAS, Jobs 2.0, explicit types, semantic matching), 2.1 (PostgreSQL, diagram enhancements, singleton tables), 2.2 (dj.Instance, thread-safe mode, graph-driven diagrams)
- `database.database_prefix` deprecated in 2.2.1 in favor of `database.name`

## Connections

- [[whats-new-2]] — 2.0 release notes
- [[whats-new-21]] — 2.1 release notes
- [[whats-new-22]] — 2.2 release notes

## Contradictions

None identified.
