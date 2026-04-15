---
title: "Manage Pipeline Project"
type: source
tags: [how-to, project-structure, best-practices]
date: 2026-04-15
source_file: raw/manage-pipeline-project.md
---

## Summary

Recommended project structure for DataJoint pipelines: `src/my_pipeline/` package with one module per schema, `datajoint.json` for config, `.secrets/` for credentials (gitignored). Schema creation order matters — create schemas with no dependencies first. Access roles (read-only analyst, read-write scientist, admin) should be granted at the database level.

## Key Claims

- One Python module per DataJoint schema: `src/my_pipeline/mouse.py`, `session.py`, `analysis.py`
- Import order determines schema creation order — import independent schemas first
- Put all schema decorators (`@schema`) in their module's `__init__` block or at module level
- Credentials in `.secrets/` — never in version control
- Database access roles: read-only (analysts), read-write (scientists), admin (database manager)
- Use `__all__` to expose table classes cleanly from each module

## Key Quotes

> "One module per schema keeps pipeline logic organized and makes schema dependencies explicit through Python import order."

## Connections

- [[configure-database]] — credential configuration
- [[manage-secrets]] — secrets management
- [[deploy-production]] — production deployment patterns
- [[Schema]] — the schema object each module creates

## Contradictions

None identified.
