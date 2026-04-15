---
title: "Computation Model"
type: concept
tags: [core-concept, computation, automation]
sources: [computation-model, run-computations, distributed-computing, handle-errors, monitor-progress]
last_updated: 2026-04-15
---

# Computation Model

DataJoint's computation model automates data processing through the `AutoPopulate` mixin implemented by `dj.Imported` and `dj.Computed` tables. The database is the scheduler — if a row doesn't exist in a Computed table, its computation hasn't run.

## Core Contract

```python
@schema
class MyComputed(dj.Computed):
    definition = """
    -> Upstream
    ---
    result : float64
    """
    
    def make(self, key):
        # 1. Fetch upstream data using key
        data = (Upstream & key).fetch1()
        # 2. Compute
        result = process(data)
        # 3. Insert results
        self.insert1({**key, 'result': result})
```

## populate() → make() Flow

1. `populate()` computes `key_source - self` (what's upstream but not yet computed)
2. For each remaining key, calls `make(key)` — exactly once
3. Jobs 2.0 (`~~table_name`) tracks state: reserved → completed / error

## Key Behaviors

- `make()` receives one primary key dict at a time
- All inserts in `make()` must be for that specific key (enforced by transaction)
- `populate(reserve_jobs=True)` enables distributed multi-worker execution
- `populate(suppress_errors=True)` stores failures in jobs table and continues
- `populate(processes=N)` runs N local parallel workers

## Three-Part Make Pattern

For long-running computations, split `make()` into three phases:
```python
def make(self, key):
    data = self.make_fetch(key)    # I/O: download from upstream
    result = self.make_compute(data)  # CPU: pure computation
    self.make_insert(key, result)  # I/O: save results
```

## Connections

- [[TableTiers]] — Imported and Computed table classes
- [[JobsSystem]] — Jobs 2.0 state tracking
- [[distributed-computing]] — multi-worker populate
- [[run-computations]] — all populate() options
