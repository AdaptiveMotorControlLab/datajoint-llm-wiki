---
title: "Configure Database"
type: source
tags: [how-to, configuration, database]
date: 2026-04-15
source_file: raw/configure-database.md
---

## Summary

Step-by-step guide for configuring DataJoint's database connection. Covers creating `datajoint.json` for non-sensitive settings, the `.secrets/` directory for credentials, MySQL and PostgreSQL configuration differences, and connection lifecycle. Introduces `dj.Instance` for isolated multi-tenant connections.

## Key Claims

- Create `datajoint.json` for host/port/backend settings; store credentials in `.secrets/database.user` and `.secrets/database.password`
- Never commit `.secrets/` — add to `.gitignore`
- PostgreSQL requires `database.backend = "postgresql"` and optionally `database.name` (new in 2.2.1)
- Default MySQL port is 3306; PostgreSQL is 5432 (auto-detected from backend setting)
- `dj.conn()` creates the global connection singleton; call explicitly or it auto-connects on first use
- `dj.Instance(host=, user=, password=)` creates isolated connections independent of global state

## Key Quotes

> "The `.secrets/` directory holds credentials as plain text files — one setting per file. Never commit this directory."

## Connections

- [[configuration]] — full configuration reference
- [[dj.Instance]] — isolated connection API
- [[manage-secrets]] — secrets management patterns

## Contradictions

None identified.
