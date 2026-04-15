---
title: "Deploy to Production"
type: source
tags: [how-to, production, deployment]
date: 2026-04-15
source_file: raw/deploy-production.md
---

## Summary

Production deployment of DataJoint pipelines requires setting `create_tables=False` to prevent accidental schema changes, using environment variables or secrets management for credentials, and careful schema versioning. Docker and Kubernetes patterns for secrets injection are covered. `database.name` (new in 2.2.1) replaces deprecated `database.database_prefix` for PostgreSQL multi-tenancy.

## Key Claims

- Set `database.create_tables = False` (or `DJ_CREATE_TABLES=false`) in production to prevent accidental table creation
- Use environment variables (`DJ_HOST`, `DJ_USER`, `DJ_PASS`) or mounted secrets for credentials — never hardcode
- `database.database_prefix` is deprecated as of 2.2.1 — use `database.name` instead for PostgreSQL
- Docker secrets can be mounted as files and read from `.secrets/` directory pattern
- Schema migrations in production require careful ordering: create new tables before dropping old ones
- Use `dj.Instance` for worker processes to avoid global state conflicts

## Key Quotes

> "In production, set `create_tables=False`. Tables should be created through a controlled migration process, not automatically on import."

## Connections

- [[configuration]] — all settings referenced
- [[dj.Instance]] — preferred for production worker processes
- [[manage-secrets]] — credential management patterns
- [[migrate-to-v20]] — migration guide for version upgrades

## Contradictions

None identified.
