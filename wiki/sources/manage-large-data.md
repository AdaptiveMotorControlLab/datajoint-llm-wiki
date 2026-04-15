---
title: "Manage Large Data"
type: source
tags: [how-to, object-storage, performance]
date: 2026-04-15
source_file: raw/manage-large-data.md
---

## Summary

Strategies for handling large datasets in DataJoint. Streaming iteration avoids loading all results into memory. `ObjectRef.open()` provides streaming access to external files. `NpyRef.load(mmap_mode='r')` memory-maps large numpy arrays. Selective fetching with projection before join reduces data transfer. Hash-addressed deduplication operates per-schema.

## Key Claims

- Use lazy/streaming iteration instead of `to_dicts()` for large result sets to avoid memory exhaustion
- `NpyRef.load(mmap_mode='r')` memory-maps the array — reads only accessed slices from disk
- `ObjectRef.open()` returns a file-like streaming object — don't load all bytes at once
- Project before joining: `Table.proj('needed_attr') * OtherTable` transfers less data
- Hash deduplication is per-schema: same content stored twice in different schemas creates two objects
- `ObjectRef.download()` downloads to local path for repeated access

## Key Quotes

> "For large numpy arrays, `mmap_mode='r'` is the right default — you access slices without loading the full array."

## Connections

- [[ObjectStorage]] — the external storage system for large data
- [[use-npy-codec]] — NpyRef API details
- [[use-object-storage]] — broader object storage patterns
- [[garbage-collection]] — reclaim space from orphaned large objects

## Contradictions

None identified.
