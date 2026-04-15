---
title: "Use Object Storage"
type: source
tags: [how-to, object-storage, api]
date: 2026-04-15
source_file: raw/use-object-storage.md
---

## Summary

Practical guide for working with DataJoint's Object-Augmented Schema (OAS). Covers hash-addressed storage for blobs/attachments, schema-addressed storage for numpy arrays and zarr objects, `staged_insert1` context manager for large uploads, `NpyRef` and `ObjectRef` APIs, and store tier configuration (named stores, default store).

## Key Claims

- Insert large objects with `with Table.staged_insert1(key) as s: s.attr = data` — handles upload before transaction commit
- Hash-addressed (`<blob@>`): content is stored as `{location}/_hash/{schema}/{hash}` — same content → same file regardless of table (within the same schema; different schemas create separate objects)
- Schema-addressed (`<npy@>`, `<object@>`): stored as `{location}/_schema/{partition}/{schema}/{table}/{key}/{field}.{token}.{ext}` — human-browsable
- `ObjectRef.open()` streams object content without full download
- `ObjectRef.download(path)` downloads to local filesystem
- Named stores: `<blob@archive>` uses the `archive` store config; `<blob@>` uses `stores.default`

## Key Quotes

> "Hash-addressed storage automatically deduplicates — store the same numpy array in 100 rows and it's stored once on disk."

## Connections

- [[ObjectStorage]] — concept page
- [[configure-storage]] — store configuration
- [[use-npy-codec]] — NpyRef API specifics
- [[garbage-collection]] — cleaning up orphaned objects

## Contradictions

None identified.
