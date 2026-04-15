---
title: "Configuration Reference"
type: source
tags: [reference, configuration, api]
date: 2026-04-15
source_file: raw/configuration.md
---

## Summary

Full reference for DataJoint configuration options. Configuration is loaded in priority order: environment variables > `.secrets/` directory > `datajoint.json` file > defaults. Key settings cover database connection (host, port, user, password, backend), external stores (S3, GCS, Azure, file), job coordination, and display options. New in 2.2: `dj.Instance` and thread-safe mode.

## Key Claims

- Priority order: env vars (highest) → `.secrets/` → `datajoint.json` → defaults (lowest)
- `database.backend` supports `mysql` (default) and `postgresql` (new in 2.1)
- Store names (`stores.<name>`) are user-defined; `stores.default` and `stores.filepath_default` are reserved defaults
- Each store has `hash_prefix` (for `<blob@>`, `<attach@>`), `schema_prefix` (for `<object@>`, `<npy@>`), and `filepath_prefix` (for `<filepath@>`)
- `jobs.add_job_metadata = True` adds hidden metadata columns to computed tables
- `dj.config.override()` context manager for temporary setting changes; use `__` for nested keys
- Thread-safe mode (`DJ_THREAD_SAFE=true`) disables global `dj.config` and `dj.conn()` — only `dj.Instance` works
- `database.create_tables = False` for production (prevents accidental schema creation)

## Key Quotes

> "In thread-safe mode, any access to `dj.config` or `dj.conn()` raises `ThreadSafetyError`. Only `dj.Instance()` works."

## Connections

- [[dj.Instance]] — isolated connection/config pair for multi-tenant use
- [[ObjectStorage]] — stores configuration section
- [[JobsSystem]] — jobs configuration section
- [[manage-secrets]] — .secrets/ directory layout

## Contradictions

None identified.
