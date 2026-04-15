---
title: "Schema"
type: entity
tags: [api, core, schema-management]
sources: [define-tables, configure-database, manage-pipeline-project, deploy-production]
last_updated: 2026-04-15
---

# Schema

The `dj.Schema` object is the bridge between Python table classes and a MySQL/PostgreSQL database schema. Created with `schema = dj.Schema('schema_name')`, then used as a decorator `@schema` to register table classes.

## API

```python
schema = dj.Schema('my_pipeline')

@schema
class MyTable(dj.Manual):
    definition = """..."""
```

## Key Behaviors

- Creates the database schema if it doesn't exist (when `create_tables=True`)
- Registers each decorated class as a table in the database
- `schema.is_activated()` checks if schema exists in the database
- `schema.list_tables()` returns all table names in the schema
- `schema.diagram()` renders the ERD for all tables in the schema
- In production: use `dj.Schema('name', create_tables=False)` to prevent accidental creation

## Connections

- [[dj.Instance]] — `inst.Schema()` creates a schema bound to an isolated connection
- [[define-tables]] — how to use @schema to register tables
- [[deploy-production]] — create_tables=False for production
