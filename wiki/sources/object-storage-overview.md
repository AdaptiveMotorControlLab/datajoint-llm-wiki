---
title: "Object Storage Overview"
type: source
tags: [conceptual, object-storage, reference]
date: 2026-04-15
source_file: raw/object-storage-overview.md
---

## Summary

Navigation guide to DataJoint's Object-Augmented Schema (OAS) documentation. Presents quick decision trees for choosing between inline storage (in DB), hash-addressed external storage (deduplicating), and schema-addressed external storage (browsable, key-based paths). Links to specific how-to guides organized by storage mode and use case.

## Key Claims

- Three storage modes: inline (in the database), hash-addressed (content-deduplicating external), schema-addressed (key-based browsable external)
- Use inline (`<blob>`, `<attach>`) for small objects that benefit from ACID transaction guarantees
- Use hash-addressed (`<blob@>`, `<attach@>`) for large objects or when deduplication is valuable
- Use schema-addressed (`<object@>`, `<npy@>`) when human-browsable paths or folder structures are needed
- Use `<filepath@>` when DataJoint should reference existing user-managed files without lifecycle control

## Key Quotes

> "The three storage modes represent different philosophical approaches to large data: in-database integrity, content-addressed deduplication, and human-readable organization."

## Connections

- [[ObjectStorage]] — the concept page synthesizing all storage information
- [[choose-storage-type]] — detailed decision guide
- [[configure-storage]] — configuration for external stores
- [[use-object-storage]] — practical usage examples

## Contradictions

None identified.
