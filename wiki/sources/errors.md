---
title: "Error Reference"
type: source
tags: [reference, errors, api]
date: 2026-04-15
source_file: raw/errors.md
---

## Summary

Reference for DataJoint's exception hierarchy rooted at `DataJointError`. Key exceptions: `LostConnectionError` (network loss), `DuplicateError` (duplicate PK insert), `IntegrityError` (FK violation), `UnknownAttributeError` (bad attribute reference), `MissingAttributeError` (required attr not provided), `MissingTableError` (table not declared), `MissingExternalFile` (orphaned external file), `BucketInaccessible` (S3 access failure).

## Key Claims

- All DataJoint exceptions inherit from `dj.DataJointError`
- `DuplicateError`: use `skip_duplicates=True` or `replace=True` on insert to avoid
- `IntegrityError`: insert parent rows before child rows to satisfy FK constraints
- `LostConnectionError`: reconnect with `dj.conn().connect()`
- `MissingExternalFile`: file was deleted outside DataJoint; check store configuration and run garbage collection
- `BucketInaccessible`: verify AWS credentials, bucket name, and region

## Key Quotes

> "Catch `dj.DataJointError` as the base class to handle all DataJoint-specific exceptions in one block."

## Connections

- [[handle-errors]] — practical guide for error handling in pipelines
- [[insert-data]] — avoiding DuplicateError and IntegrityError
- [[ObjectStorage]] — MissingExternalFile and BucketInaccessible

## Contradictions

None identified.
