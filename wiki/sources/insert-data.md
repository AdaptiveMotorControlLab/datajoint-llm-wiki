---
title: "Insert Data"
type: source
tags: [how-to, data-management, insert]
date: 2026-04-15
source_file: raw/insert-data.md
---

## Summary

Guide for inserting data into DataJoint tables. `insert1(dict)` inserts a single row; `insert(list_of_dicts)` inserts multiple rows in one transaction. DataFrames can be inserted directly. Key parameters: `skip_duplicates` (silently skip), `replace` (overwrite existing), `ignore_extra_fields` (drop unknown columns). Master-part inserts must be atomic transactions.

## Key Claims

- `insert1({'attr': value, ...})` is the primary single-row insert method
- `insert([...])` inserts a list of row dicts in a single transaction — more efficient than loop of insert1
- `table.insert(dataframe)` inserts a pandas DataFrame directly
- `skip_duplicates=True` ignores rows whose primary key already exists (no error, no update)
- `replace=True` overwrites existing rows with the same primary key
- `ignore_extra_fields=True` drops DataFrame columns not in the table definition
- Master-part inserts should use `with master.connection.transaction():` for atomicity
- `insert(query_expression)` inserts results from another query — efficient bulk insert

## Key Quotes

> "Master and part table inserts should always happen in a single transaction — partial inserts leave the pipeline in an inconsistent state."

## Connections

- [[errors]] — DuplicateError and IntegrityError from bad inserts
- [[ComputationModel]] — make() calls insert1/insert to save results
- [[define-tables]] — table structure that determines valid inserts

## Contradictions

None identified.
