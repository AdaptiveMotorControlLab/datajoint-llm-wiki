---
title: "dj.Instance"
type: entity
tags: [api, multi-tenant, thread-safety]
sources: [use-instances, configuration, whats-new-22, configure-database]
last_updated: 2026-04-15
---

# dj.Instance

Introduced in DataJoint 2.2. Encapsulates an isolated Config + Connection pair, independent of global `dj.config` and `dj.conn()`. The preferred API for multi-tenant applications, concurrent test suites, and thread-safe worker processes.

## API

```python
inst = dj.Instance(
    host="localhost",
    user="root",
    password="secret",
    port=3306,           # optional
    use_tls=None,        # optional
    safemode=False,      # any config override via kwargs
)

schema = inst.Schema("my_schema")
table = inst.FreeTable("my_schema.my_table")
```

## Key Properties

- `inst.config` — the instance's Config object
- `inst.connection` — the instance's Connection object
- Thread-safe: each instance is completely independent

## Use Cases

- **Flask/FastAPI**: one Instance per request → no shared state between requests
- **pytest**: one Instance per test fixture → clean isolation
- **Worker processes**: each worker gets its own Instance → no global lock contention
- **Thread-safe mode**: only `dj.Instance` works when `DJ_THREAD_SAFE=true`

## Connections

- [[Schema]] — `inst.Schema()` creates a schema bound to this instance
- [[configuration]] — thread-safe mode configuration
- [[use-instances]] — practical guide
