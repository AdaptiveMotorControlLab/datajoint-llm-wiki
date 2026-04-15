---
title: "Choose Storage Type"
type: source
tags: [how-to, object-storage, reference]
date: 2026-04-15
source_file: raw/choose-storage-type.md
---

## Summary

A decision guide for selecting among DataJoint's codec types: `<blob>` (small objects in DB), `<blob@>` (hash-addressed external), `<attach>` (files in DB), `<attach@>` (files external), `<npy@>` (numpy arrays with lazy access), `<object@>` (schema-addressed folders like zarr), `<filepath@>` (user-managed file references). Choice depends on size, access pattern, deduplication needs, and whether data is managed or referenced.

## Key Claims

- Use `<blob>` for objects under ~1MB that need atomic DB storage; use `<blob@>` for anything larger
- Use `<npy@>` specifically for numpy arrays when lazy loading (mmap) or metadata inspection without full download is needed
- Use `<object@>` for hierarchical data structures (zarr groups) requiring folder addressing
- Use `<filepath@>` when DataJoint should reference existing files but not manage their lifecycle
- `<attach>` stores entire files in the database — appropriate only for small files (configs, logs, reports)
- Hash-addressed types (`<blob@>`, `<attach@>`) deduplicate content across the entire schema

## Key Quotes

> "If you're storing numpy arrays and care about shape/dtype metadata or lazy loading, use `<npy@>`. If you just need to store arbitrary Python objects, use `<blob@>`."

## Connections

- [[ObjectStorage]] — parent concept covering all external storage approaches
- [[use-npy-codec]] — NpyRef: the object returned by `<npy@>` columns
- [[use-object-storage]] — ObjectRef: base class for external storage references
- [[garbage-collection]] — external stores require periodic gc to reclaim orphaned objects

## Contradictions

None identified.
