---
title: "Manage Secrets"
type: source
tags: [how-to, configuration, security]
date: 2026-04-15
source_file: raw/manage-secrets.md
---

## Summary

DataJoint uses a layered secrets management system. Configuration priority: programmatic (dj.config.x = y) > environment variables > `.secrets/` directory > `datajoint.json` > defaults. The `.secrets/` directory contains one credential per file (e.g., `database.user`, `stores.main.access_key`). Environment variables are preferred for CI/CD.

## Key Claims

- Priority (highest to lowest): programmatic assignment > env vars > `.secrets/` > `datajoint.json` > defaults
- `.secrets/` file names map to dotted config paths: `stores.main.access_key` → file named `stores.main.access_key`
- Environment variables: `DJ_HOST`, `DJ_USER`, `DJ_PASS`, `DJ_USE_TLS`, `DJ_BACKEND`, `DJ_DATABASE_NAME`
- Per-store credentials must use `datajoint.json` or `.secrets/` — no env var support for nested store config
- Always add `.secrets/` to `.gitignore`
- In CI/CD: use environment variables injected by the CI system, not committed files

## Key Quotes

> "The `.secrets/` directory is the recommended way to manage credentials in development — one file per setting, never committed to version control."

## Connections

- [[configuration]] — full configuration reference
- [[configure-database]] — database credential patterns
- [[deploy-production]] — production secrets management

## Contradictions

None identified.
