---
title: "Monitor Progress"
type: source
tags: [how-to, computation, monitoring]
date: 2026-04-15
source_file: raw/monitor-progress.md
---

## Summary

Guide for monitoring DataJoint computation progress. `populate(display_progress=True)` shows a tqdm progress bar. `table.jobs.progress()` returns dict with counts by status. `key_source - table` gives remaining unprocessed keys. Job metadata columns (enabled via `jobs.add_job_metadata`) provide per-row timing and worker information.

## Key Claims

- `populate(display_progress=True)` shows live progress bar during populate
- `table.jobs.progress()` returns `{'reserved': N, 'pending': N, 'error': N, 'completed': N}`
- `(table.key_source - table)` is the set of remaining keys not yet computed
- `jobs.add_job_metadata = True` adds `_job_reserved`, `_job_completed`, `_job_duration`, `_job_worker` hidden columns
- Job metadata enables monitoring which worker processed each row and how long it took
- `table.jobs.reserved` / `.errors` / `.completed` are query expressions for each status

## Key Quotes

> "The most direct way to check remaining work: `len(MyTable.key_source - MyTable)` — the anti-join of what's computed from what needs to be computed."

## Connections

- [[ComputationModel]] — the execution model being monitored
- [[JobsSystem]] — the jobs tables that store status
- [[distributed-computing]] — monitoring in multi-worker scenarios

## Contradictions

None identified.
