---
title: "Installation"
type: source
tags: [how-to, installation, setup]
date: 2026-04-15
source_file: raw/installation.md
---

## Summary

DataJoint requires Python 3.10+ and MySQL 8.0+ (or PostgreSQL). Core install: `pip install datajoint`. Optional dependencies for visualization (`datajoint[viz]`), polars integration (`datajoint[polars]`), and cloud storage (`datajoint[s3]`, `datajoint[gcs]`, `datajoint[azure]`). Docker Compose provided for local MySQL setup.

## Key Claims

- Minimum requirements: Python 3.10+, MySQL 8.0+ or PostgreSQL 15+
- Core install: `pip install datajoint` — includes PyMySQL, numpy, tqdm
- Optional extras: `datajoint[viz]` (matplotlib/graphviz for ERDs), `datajoint[polars]`, `datajoint[s3]`, `datajoint[gcs]`, `datajoint[azure]`
- Docker MySQL quickstart: `docker run -e MYSQL_ROOT_PASSWORD=secret -p 3306:3306 mysql:8.0`
- Full tutorial environment: `docker compose up -d` in datajoint-docs repo

## Connections

- [[configure-database]] — next step after installation
- [[installation]] — this is the installation source page itself

## Contradictions

None identified.
