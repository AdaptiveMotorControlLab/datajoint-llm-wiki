---
title: "What's New in DataJoint 2.1"
type: source
tags: [changelog, release-notes]
date: 2026-04-15
source_file: raw/whats-new-21.md
---

## Summary

DataJoint 2.1 adds PostgreSQL 15+ support (set `database.backend = "postgresql"`), diagram enhancements (configurable direction LR/TB, Mermaid output, schema grouping, collapse), and singleton tables. New `DJ_DIAGRAM_DIRECTION` environment variable. `DJ_USE_TLS` env var for TLS configuration.

## Key Changes

- **PostgreSQL support**: `database.backend = "postgresql"` (or `DB_BACKEND=postgresql`); port auto-detected as 5432
- **Diagram direction**: `display.diagram_direction = "LR"` (default) or `"TB"`; env var `DJ_DIAGRAM_DIRECTION`
- **Mermaid output**: `schema.diagram().mermaid()` for Mermaid-format ERD
- **Schema grouping**: Diagram can group tables by schema with visual boundaries
- **Collapse**: `Diagram.collapse(table)` collapses a subtree in the ERD
- **Singleton tables**: Tables with no primary key (single-row tables) now supported
- **`DJ_USE_TLS` env var**: Configure TLS from environment

## Key Quotes

> "PostgreSQL support in 2.1 opens DataJoint to labs already running Postgres — same API, just change the backend setting."

## Connections

- [[configuration]] — new backend and diagram settings
- [[whats-new-2]] — previous major release
- [[whats-new-22]] — next release

## Contradictions

None identified.
