---
title: "Distributed Computing"
type: source
tags: [how-to, computation, distributed]
date: 2026-04-15
source_file: raw/distributed-computing.md
---

## Summary

DataJoint supports distributed parallel execution through `populate(reserve_jobs=True)`. Workers reserve jobs atomically from the per-table `~~table_name` jobs table, process them, and mark as completed or error. Multiple workers can run simultaneously without coordination — the database acts as the job queue. Priority (0-255, lower = more urgent) and process count can be configured.

## Key Claims

- `populate(reserve_jobs=True)` enables atomic job reservation — multiple workers can run safely
- Each worker reserves one key at a time; competing workers see `reserved` status and skip it
- Job states: `reserved` → `completed` or `error` (with full traceback)
- `populate(processes=N)` launches N worker processes on a single machine
- Priority is set per-key in key_source; lower priority values are processed first (0 = most urgent)
- Orphaned `reserved` jobs (worker died) auto-expire after `jobs.stale_timeout` seconds (default 3600)
- `populate(order="random")` randomizes key order for better load distribution

## Key Quotes

> "Workers compete fairly for jobs through atomic database reservation — no external message queue needed."

## Connections

- [[ComputationModel]] — the make/populate execution model
- [[JobsSystem]] — the per-table jobs tables and state machine
- [[monitor-progress]] — tracking distributed execution progress

## Contradictions

None identified.
