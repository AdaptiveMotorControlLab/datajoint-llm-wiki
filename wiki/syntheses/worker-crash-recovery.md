---
title: "What Happens When a Worker Crashes with reserve_jobs=True"
type: synthesis
tags: [computation, distributed, jobs, error-handling, crash-recovery]
sources: [distributed-computing, handle-errors, computation-model]
last_updated: 2026-04-17
---

## What Happens When a Worker Crashes with `reserve_jobs=True`

### The Job Stays `reserved`

When `populate(reserve_jobs=True)` is used, each worker atomically claims a key by writing a `reserved` row to the per-table jobs table (`~~analysis`, `~~my_table`, etc.) before calling `make()`. If the worker process dies mid-computation — OOM kill, network disconnect, node failure — the row stays in `reserved` state indefinitely. No exception is raised, no error is written, and no other worker will pick up that key while it shows `reserved`.

Per [[JobsSystem]], the job state machine is:

```
pending → reserved → completed
                   → error
```

A crashed worker leaves the job stuck between `reserved` and either outcome.

---

### Automatic Recovery: `stale_timeout`

[[distributed-computing]] documents the recovery mechanism:

> Orphaned `reserved` jobs (worker died) auto-expire after `jobs.stale_timeout` seconds (default **3600 s**).

Once the timeout elapses, the `reserved` row is treated as expired. The next `populate()` call from any remaining worker will re-claim the key and restart `make()` from scratch.

**Tune `stale_timeout` to match your longest `make()` runtime.** If your computation takes 3.5 hours (~12 600 s) but `stale_timeout` is 3 600 s, a slow-but-alive worker will have its job stolen by another worker before it finishes. Set the timeout comfortably above your worst-case `make()` duration:

```json
{
    "jobs": {
        "stale_timeout": 14400
    }
}
```

Or in Python at startup:

```python
dj.config['jobs.stale_timeout'] = 14400   # 4 hours, in seconds
```

---

### Inspecting Stuck Jobs

While waiting for `stale_timeout` to expire you can inspect the jobs table directly:

```python
# See all currently reserved (possibly stuck) jobs
Analysis.jobs.reserved.fetch(as_dict=True)
# Returns rows with: key columns, status='reserved', host, pid, timestamp

# Force-release a stuck reservation manually (use with care):
del Analysis.jobs.reserved & {'host': 'dead-worker-hostname'}
```

After manual deletion the key immediately becomes eligible for re-claim.

---

### Errors vs. Crashes: Two Different States

[[handle-errors]] distinguishes between a `make()` that raises an exception and a worker that crashes:

| Scenario | Job state | Recovery |
|---|---|---|
| `make()` raises + `suppress_errors=True` | `error` | `Analysis.jobs.errors.fetch(as_dict=True)` to inspect; `del Analysis.jobs.errors` to retry |
| Worker process killed mid-computation | `reserved` | Auto-expiry after `stale_timeout`; or manual deletion |

Inspect failed jobs after a run:

```python
# Jobs that errored (exception was caught):
Analysis.jobs.errors.fetch(as_dict=True)   # includes full Python traceback

# Retry all errored jobs:
del Analysis.jobs.errors
```

---

### Recommended Production Populate Call

Per [[long-duration-make]], combining all three options gives full resilience:

```python
Analysis.populate(
    reserve_jobs=True,    # atomic reservation — safe for concurrent workers
    order="random",       # spread load; avoid all workers picking the same key
    suppress_errors=True, # log make() exceptions, don't halt the worker fleet
    display_progress=True,
)
```

And in config before launching workers:

```python
dj.config['jobs.stale_timeout'] = 14400   # tune to > your longest make() runtime
```

---

## Sources

- [[JobsSystem]] — per-table `~~table_name` job tables, states, and `stale_timeout` configuration
- [[distributed-computing]] — `reserve_jobs=True`, multi-worker execution, stale reservation expiry
- [[handle-errors]] — `suppress_errors`, `jobs.errors`, retry patterns
- [[long-duration-make]] — synthesis: crash recovery, `stale_timeout` tuning, production populate call
