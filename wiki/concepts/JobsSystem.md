---
title: "Jobs System (Jobs 2.0)"
type: concept
tags: [core-concept, computation, distributed]
sources: [computation-model, distributed-computing, handle-errors, monitor-progress, run-computations, whats-new-2]
last_updated: 2026-04-15
---

# Jobs System (Jobs 2.0)

DataJoint's job coordination system tracks computation state for each Computed/Imported table. Introduced as "Jobs 2.0" in DataJoint 2.0, replacing the global `~jobs` table with per-table `~~table_name` tables.

## Architecture

Each computed table `MyTable` gets a corresponding jobs table `~~my_table` created automatically. This enables:
- Per-table job isolation (different tables don't share a queue)
- Atomic reservation by multiple workers
- Full error tracking with tracebacks

## Job States

```
pending → reserved → completed
                   → error
```

- **pending**: key exists in key_source but not yet processed
- **reserved**: a worker has claimed this key (atomic reservation)
- **completed**: `make()` completed successfully (only stored if `jobs.keep_completed=True`)
- **error**: `make()` raised an exception (full traceback stored)

## Key API

```python
# Access jobs table
MyTable.jobs              # query expression for all jobs
MyTable.jobs.reserved     # currently reserved keys
MyTable.jobs.errors       # failed jobs with tracebacks
MyTable.jobs.progress()   # dict: {reserved: N, error: N, ...}

# Retry failed jobs
del MyTable.jobs.errors   # clear errors → keys are eligible for retry
```

## Configuration

```json
{
    "jobs": {
        "stale_timeout": 3600,        // seconds before reserved job expires
        "keep_completed": false,       // retain success records
        "default_priority": 5,         // 0=most urgent, 255=least urgent
        "add_job_metadata": false,     // add hidden metadata columns to table
        "version_method": null         // git, none, or null (disabled)
    }
}
```

## Connections

- [[ComputationModel]] — jobs track execution of make()
- [[distributed-computing]] — reserve_jobs=True uses this system
- [[handle-errors]] — accessing and managing error records
- [[monitor-progress]] — monitoring via jobs tables
