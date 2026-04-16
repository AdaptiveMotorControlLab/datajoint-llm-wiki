---
title: "How to Deal with Long-Duration make() Computations"
type: synthesis
tags: [computation, populate, distributed, error-handling, monitoring]
sources: [computation-model, run-computations, distributed-computing, handle-errors, monitor-progress]
last_updated: 2026-04-16
---

## How to Deal with Long-Duration `make()` Computations

Long-running `make()` functions introduce three practical problems: they hold a database transaction open for a long time, they make it hard to parallelize work, and failures leave no trace of what went wrong. DataJoint has a coherent set of tools for each.

---

### 1. Structure `make()` as Three Phases

[[ComputationModel]] recommends splitting any long `make()` into three clearly separated phases:

```python
@schema
class Analysis(dj.Computed):
    definition = """
    -> Session
    ---
    result : float64
    """

    def make(self, key):
        data   = self.make_fetch(key)     # Phase 1 — I/O: fetch from upstream
        result = self.make_compute(data)  # Phase 2 — CPU: pure computation
        self.make_insert(key, result)     # Phase 3 — I/O: insert results

    def make_fetch(self, key):
        return (Session & key).fetch1()

    def make_compute(self, data):
        return expensive_algorithm(data)

    def make_insert(self, key, result):
        self.insert1({**key, 'result': result})
```

This matters because:
- Phase 2 (CPU work) can be cancelled or restarted without corrupting the database
- You can profile each phase independently
- The database transaction is only open during Phase 3, not across the entire computation

---

### 2. Distribute Across Workers

For truly long pipelines, don't run one worker sequentially — use [[distributed-computing]] to let multiple processes claim jobs atomically:

```python
# Worker process (run this on N machines or in N processes)
MyTable.populate(
    reserve_jobs=True,    # atomic DB reservation — safe for concurrent workers
    order="random",       # randomise to avoid all workers picking the same keys
    suppress_errors=True, # don't halt the worker on a single failure
    display_progress=True
)
```

Per [[JobsSystem]], each call to `populate(reserve_jobs=True)` claims one key at a time from the per-table `~~my_table` jobs table. Competing workers see `reserved` status and skip it — no external message queue needed.

To launch N parallel workers on one machine:
```python
MyTable.populate(reserve_jobs=True, processes=4)
```

---

### 3. Handle Stale Reservations (Worker Crashes)

If a worker dies mid-computation, its job stays in `reserved` state. [[JobsSystem]] auto-expires stale reservations after `jobs.stale_timeout` seconds (default 3600). You can tune this:

```json
{
    "jobs": {
        "stale_timeout": 7200
    }
}
```

Expired `reserved` jobs become eligible for other workers to pick up on the next `populate()` call.

---

### 4. Track Errors Without Stopping the Run

Per [[handle-errors]], always use `suppress_errors=True` in production so that one bad key doesn't stop all remaining work:

```python
MyTable.populate(reserve_jobs=True, suppress_errors=True)

# Later — inspect what failed:
MyTable.jobs.errors.fetch(as_dict=True)  # includes full Python traceback

# Retry all failed jobs:
del MyTable.jobs.errors
```

The full traceback is stored in `error_message` / `error_stack` columns of `~~my_table`.

---

### 5. Monitor Progress Without Blocking

[[monitor-progress]] gives you several ways to observe a running pipeline without interrupting it:

```python
# Remaining work (works even without reserve_jobs):
remaining = len(MyTable.key_source - MyTable)

# Status breakdown (requires reserve_jobs=True):
MyTable.jobs.progress()
# → {'reserved': 3, 'pending': 47, 'error': 2, 'completed': 121}

# Per-row timing — enable in config once:
# "add_job_metadata": true
# Each row then gets hidden _job_duration, _job_worker, _job_reserved, _job_completed columns
```

---

### 6. Control Which Keys Are Processed

Override `key_source` to process a subset or reprioritise:

```python
@property
def key_source(self):
    return Session & 'session_date > "2026-04-01"'
```

Or pass a restriction directly to `populate()`:

```python
MyTable.populate(Session & {'subject_id': 'mouse_01'})
```

---

### Summary Decision Tree

```
Long make() function?
│
├─ Structure it — use three-part make (fetch / compute / insert)
│
├─ Multiple workers? → populate(reserve_jobs=True, order="random")
│
├─ Local parallelism only? → populate(processes=N, reserve_jobs=True)
│
├─ Don't halt on failures → suppress_errors=True
│
├─ Monitor without blocking → jobs.progress(), key_source - table
│
└─ Workers may crash? → tune stale_timeout in jobs config
```

---

## Sources

- [[ComputationModel]] — three-part make pattern, populate/make flow
- [[run-computations]] — all populate() options
- [[distributed-computing]] — reserve_jobs, multi-worker execution, stale timeout
- [[handle-errors]] — suppress_errors, jobs.errors, retry patterns
- [[monitor-progress]] — display_progress, jobs.progress(), key_source anti-join
- [[JobsSystem]] — per-table job tables, states, configuration
