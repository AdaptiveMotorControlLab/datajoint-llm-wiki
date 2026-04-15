---
title: "Computation Model"
type: source
tags: [conceptual, computation, automation]
date: 2026-04-15
source_file: raw/computation-model.md
---

## Summary

DataJoint's computation model automates data processing through `Imported` and `Computed` table classes that implement `AutoPopulate`. The `populate()` method finds un-processed keys from `key_source` and calls `make(key)` once per key. Jobs 2.0 tracks processing state (reserved, completed, error) per table in `~~table_name` tables and enables distributed execution with `reserve_jobs=True`.

## Key Claims

- `make(key)` receives exactly one primary key dict; all inserts within `make()` are implicitly for that key
- `key_source` defaults to the full upstream join but can be overridden for custom filtering
- Jobs 2.0 stores job state per-table in `~~table_name` automatically created tables
- Three-part make pattern (`make_fetch` / `make_compute` / `make_insert`) separates I/O from computation for long-running jobs
- Job metadata (worker ID, host, start time, duration) can be added to computed tables with `jobs.add_job_metadata = True`
- `suppress_errors=True` continues populate() even if individual `make()` calls fail

## Key Quotes

> "`make()` receives a key dict with all primary key attributes. The table knows its identity — use that key to fetch upstream data and insert results."

> "Jobs 2.0 replaces the legacy `~jobs` table with per-table `~~table_name` tables. Each computed table gets its own job coordinator."

## Connections

- [[TableTiers]] — Computed and Imported table classes that implement AutoPopulate
- [[JobsSystem]] — Jobs 2.0 state machine and coordination
- [[distributed-computing]] — reserve_jobs=True enables multi-worker execution
- [[ComputationModel]] — concept page for this topic

## Contradictions

None identified.
