---
title: "Run Computations"
type: source
tags: [how-to, computation, populate]
date: 2026-04-15
source_file: raw/run-computations.md
---

## Summary

Practical guide for running DataJoint computations with `populate()`. Covers all populate() options (reserve_jobs, processes, suppress_errors, display_progress, order), when to use reserve_jobs vs simple parallel processes, the three-part make pattern for long-running computations, and custom key_source patterns.

## Key Claims

- `populate()` options: `reserve_jobs` (distributed-safe), `processes` (local parallelism), `suppress_errors` (continue on failure), `display_progress` (tqdm bar), `order` ('sequential'/'random')
- Use `reserve_jobs=True` when multiple workers from different machines/processes will populate the same table
- `processes=N` spawns N local workers — combine with `reserve_jobs=True` for safety
- Three-part make pattern: `make_fetch()` (download data), `make_compute()` (CPU work), `make_insert()` (save results) — allows interrupting at natural boundaries
- Custom `key_source` property can filter or reorder processing: `@property def key_source(self): return upstream_table & condition`
- `populate(restriction)` applies an additional restriction to key_source

## Key Quotes

> "Use `reserve_jobs=True` whenever more than one process might call `populate()` on the same table — without it, two workers will double-process the same key."

## Connections

- [[ComputationModel]] — the full computation model
- [[distributed-computing]] — distributed execution details
- [[handle-errors]] — handling populate() failures
- [[monitor-progress]] — monitoring populate() progress

## Contradictions

None identified.
