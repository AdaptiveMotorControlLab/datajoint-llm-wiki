---
title: "Backup and Restore"
type: source
tags: [how-to, operations, backup]
date: 2026-04-15
source_file: raw/backup-restore.md
---

## Summary

DataJoint databases can be backed up with `mysqldump` for relational data and separate strategies for external object storage (S3 sync, filesystem snapshots). Restore order must respect foreign key dependencies: restore parent tables before child tables. Schema prefixes help identify related tables.

## Key Claims

- Use `mysqldump --single-transaction` for consistent MySQL backups without locking
- External stores (blobs, attachments) must be backed up separately from the database
- When restoring, recreate schema/databases first, then restore tables in dependency order
- S3 buckets can be synced with `aws s3 sync` for object storage backup
- DataJoint's `schema.list_tables()` or the ERD can help determine restore order

## Key Quotes

> "Restore parent tables before child tables to avoid foreign key constraint violations."

## Connections

- [[Schema]] — identifies tables belonging to a schema for backup grouping
- [[ObjectStorage]] — object stores (S3, file) require separate backup strategies
- [[garbage-collection]] — related: orphan cleanup before backup reduces backup size

## Contradictions

None identified.
