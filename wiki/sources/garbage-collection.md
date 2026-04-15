---
title: "Garbage Collection"
type: source
tags: [how-to, object-storage, maintenance]
date: 2026-04-15
source_file: raw/garbage-collection.md
---

## Summary

External DataJoint stores can accumulate orphaned objects (files with no corresponding database row) due to failed inserts, deleted rows, or interrupted transactions. `dj.gc.scan()` identifies orphans; `dj.gc.collect(dry_run=False)` deletes them. Hash-addressed orphans are detected by cross-referencing stored hashes against all tables; schema-addressed orphans use path-based detection.

## Key Claims

- Orphaned objects occur when: a row is deleted, an insert fails after writing to the store, or a transaction is interrupted
- `dj.gc.scan()` returns a report of orphaned objects per store without deleting anything
- `dj.gc.collect(dry_run=True)` (default) shows what would be deleted; `dry_run=False` actually deletes
- Hash-addressed storage (`<blob@>`, `<attach@>`) detects orphans by checking hash existence in all tables
- Schema-addressed storage (`<object@>`, `<npy@>`) detects orphans by checking key paths against table rows
- Recommended to run garbage collection periodically (e.g., weekly cron job) on active pipelines

## Key Quotes

> "Always run `dry_run=True` first to verify the orphan list before committing to deletion."

## Connections

- [[ObjectStorage]] — the external stores that accumulate orphans
- [[configure-storage]] — store configuration
- [[backup-restore]] — run GC before backup to reduce backup size

## Contradictions

None identified.
