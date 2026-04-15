---
title: "Use the NPY Codec"
type: source
tags: [how-to, object-storage, numpy]
date: 2026-04-15
source_file: raw/use-npy-codec.md
---

## Summary

The `<npy@>` codec stores numpy arrays as external NPY files with rich metadata access. `NpyRef` objects (returned on fetch) expose `.shape`, `.dtype`, `.ndim`, `.size`, `.nbytes` without downloading the full array. `.load()` downloads the array; `.load(mmap_mode='r')` memory-maps it for lazy slice access. More efficient than `<blob@>` for large arrays when metadata inspection is needed.

## Key Claims

- `<npy@>` stores arrays in NPY format (numpy native) with schema-addressed paths
- Fetching a `<npy@>` column returns `NpyRef` objects, not numpy arrays directly
- `NpyRef` properties: `.shape` (tuple), `.dtype` (numpy dtype), `.ndim`, `.size`, `.nbytes` — no download needed
- `ref.load()` downloads and returns the full numpy array
- `ref.load(mmap_mode='r')` returns a memory-mapped array — slices read from disk on demand
- Bulk fetch: fetching many NpyRef objects is safe (returns refs, not arrays) — call `.load()` selectively
- `<npy@>` vs `<blob@>`: npy is schema-addressed (browsable paths), blob is hash-addressed (content-deduplicating); choose npy for large arrays needing lazy access

## Key Quotes

> "Fetch the NpyRef first, inspect `.shape` and `.dtype`, then decide whether to load. You avoid downloading gigabytes just to check array dimensions."

## Connections

- [[ObjectStorage]] — the external storage system
- [[choose-storage-type]] — when to use npy vs blob
- [[manage-large-data]] — patterns for large array workflows

## Contradictions

None identified.
