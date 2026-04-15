---
title: "Data Pipelines"
type: source
tags: [conceptual, architecture, pipelines]
date: 2026-04-15
source_file: raw/data-pipelines.md
---

## Summary

A DataJoint data pipeline consists of three components: a code repository (table definitions + `make()` functions), a relational database (MySQL or PostgreSQL), and an optional object store (for large data). The pipeline's DAG structure enforces data dependency ordering. DataJoint's Object-Augmented Schema (OAS) unifies relational and object storage under a single schema.

## Key Claims

- A pipeline = code repo + relational DB + optional object store — all three are equally important
- DAG constraints: no cycles, no lateral dependencies — each table depends only on upstream tables
- `make()` calls fetch upstream data, compute results, and insert into the current table
- The OAS philosophy: treat large objects as first-class schema citizens, not external blobs with text pointers
- Workflow steps are tables; completing a step means all rows are computed
- Pipelines compose: output of one pipeline becomes input to another via shared schemas

## Key Quotes

> "A DataJoint pipeline is a data dependency graph where each node is a table, each edge is a foreign key, and each computation is a `make()` function."

> "The Object-Augmented Schema extends the relational model to treat large objects as integral parts of the schema, not external appendages."

## Connections

- [[ComputationModel]] — how make() and populate() implement pipeline execution
- [[ObjectStorage]] — the OAS system for large data
- [[TableTiers]] — how tier classification maps to pipeline roles
- [[RelationalWorkflowModel]] — the theoretical foundation

## Contradictions

None identified.
