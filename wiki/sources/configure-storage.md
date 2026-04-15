---
title: "Configure Storage"
type: source
tags: [how-to, configuration, object-storage]
date: 2026-04-15
source_file: raw/configure-storage.md
---

## Summary

Guide for configuring DataJoint's external object stores for S3, MinIO, Google Cloud Storage, Azure Blob Storage, and local filesystem. Each store has a name and protocol-specific settings. Subfolding (e.g., `[2,2]`) prevents filesystem issues with large numbers of files. Partition patterns organize schema-addressed storage by key attributes.

## Key Claims

- Store names are user-defined; reference them with `<blob@store_name>`, `<npy@store_name>`, etc.
- Omitting store name in codec uses `stores.default`; for `<filepath@>` uses `stores.filepath_default`
- `subfolding: [2, 2]` splits hash-addressed paths into 2-char subdirectory levels (e.g., `ab/cd/abcd...`)
- `partition_pattern: "subject_id/session_date"` creates browsable directory structure for schema-addressed storage
- S3 credentials should be in `.secrets/` not in `datajoint.json`
- `hash_prefix`, `schema_prefix`, and `filepath_prefix` must be mutually exclusive path prefixes

## Key Quotes

> "Subfolding prevents directories from becoming too large — use `[2, 2]` for stores with millions of objects."

## Connections

- [[ObjectStorage]] — the object storage system this configures
- [[configuration]] — full configuration reference
- [[garbage-collection]] — external stores need periodic cleanup

## Contradictions

None identified.
