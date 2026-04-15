# Wiki Index

This file is maintained by the LLM. Updated on every ingest.

## Overview
- [Overview](overview.md) — living synthesis across all sources

## Sources

### Architecture & Theory
- [Data Pipelines](sources/data-pipelines.md) — pipeline = code + DB + object store; OAS philosophy
- [Relational Workflow Model](sources/relational-workflow-model.md) — Codd + Chen + workflow unified
- [Query Algebra Specification](sources/query-algebra.md) — formal algebra with algebraic closure
- [Computation Model](sources/computation-model.md) — AutoPopulate, make(), Jobs 2.0
- [Normalization](sources/normalization.md) — workflow normalization and intrinsic attributes principles
- [Entity Integrity](sources/entity-integrity.md) — primary key theory, dimensions, attribute lineage
- [Table Tiers Diagram](sources/concepts-table-tiers-diagram.md) — visual pipeline with color-coded tiers
- [FAQ](sources/faq.md) — why DataJoint vs SQL/ORM/Airflow/lakehouses

### Reference / API
- [Query Operators Reference](sources/operators.md) — all 7 operators, dj.Top, dj.U, precedence
- [Table Definition Syntax](sources/definition-syntax.md) — formal grammar for definition strings
- [Configuration Reference](sources/configuration.md) — all settings, stores, jobs, display
- [Error Reference](sources/errors.md) — exception hierarchy and resolution
- [Type System](sources/type-system.md) — three-layer architecture, core types, codec types
- [Query Data Quick Reference](sources/query-data.md) — cheat sheet for all operators
- [Fetch Results](sources/fetch-results.md) — to_dicts, to_pandas, fetch1, streaming
- [Versioning Policy](sources/versioning.md) — 2.0 baseline, version annotations
- [Publications](sources/publications.md) — 100+ papers using DataJoint
- [Citation Guidelines](sources/citation.md) — how to cite DataJoint
- [Tutorials Index](sources/index.md) — learning paths and tutorial listing

### How-To Guides
- [Installation](sources/installation.md) — pip install, optional deps, Docker MySQL
- [Configure Database](sources/configure-database.md) — datajoint.json, .secrets/, MySQL/PostgreSQL
- [Configure Storage](sources/configure-storage.md) — S3, GCS, Azure, local file stores
- [Manage Secrets](sources/manage-secrets.md) — priority order, .secrets/, env vars
- [Define Tables](sources/define-tables.md) — @schema decorator, tier classes, FK modifiers
- [Insert Data](sources/insert-data.md) — insert1, insert, DataFrame, skip_duplicates, replace
- [Query Data](sources/query-data.md) — quick reference for all operators
- [Fetch Results](sources/fetch-results.md) — all fetch methods, order_by/limit/offset
- [Delete Data](sources/delete-data.md) — cascade delete, safemode, Diagram.cascade()
- [Alter Tables](sources/alter-tables.md) — alter(), supported and unsupported changes
- [Run Computations](sources/run-computations.md) — populate() options, three-part make
- [Handle Errors](sources/handle-errors.md) — suppress_errors, jobs.errors, retry patterns
- [Monitor Progress](sources/monitor-progress.md) — display_progress, jobs.progress(), key_source
- [Distributed Computing](sources/distributed-computing.md) — reserve_jobs, multi-worker
- [Use Object Storage](sources/use-object-storage.md) — OAS, staged_insert1, ObjectRef API
- [Use NPY Codec](sources/use-npy-codec.md) — NpyRef, lazy loading, mmap_mode
- [Use dj.Instance](sources/use-instances.md) — isolated connections, multi-tenant patterns
- [Use DataJoint CLI](sources/use-cli.md) — dj REPL, schema loading, interactive queries
- [Use Plugin Codecs](sources/use-plugin-codecs.md) — entry points, dj-zarr-codecs, versioning
- [Manage Large Data](sources/manage-large-data.md) — streaming, mmap, selective fetching
- [Manage Pipeline Project](sources/manage-pipeline-project.md) — project structure, access roles
- [Design Primary Keys](sources/design-primary-keys.md) — natural keys, composite keys, UUIDs
- [Create Custom Codec](sources/create-custom-codec.md) — dj.Codec subclass, encode/decode
- [Backup and Restore](sources/backup-restore.md) — mysqldump, S3 sync, restore order
- [Garbage Collection](sources/garbage-collection.md) — dj.gc.scan(), dj.gc.collect()
- [Deploy to Production](sources/deploy-production.md) — create_tables=False, secrets, Docker/K8s
- [Testing DataJoint Pipelines](sources/testing.md) — real DBs, testcontainers, pytest fixtures
- [Choose Storage Type](sources/choose-storage-type.md) — decision guide for all codec types
- [Object Storage Overview](sources/object-storage-overview.md) — navigation guide, decision trees
- [Custom Codecs](sources/custom-codecs.md) — codec system, chaining, plugin packages
- [Semantic Matching](sources/semantic-matching.md) — homologous attributes, lineage, resolution

### Object Storage
- [Object Storage Overview](sources/object-storage-overview.md) — navigation guide
- [Choose Storage Type](sources/choose-storage-type.md) — decision guide

### Changelogs
- [What's New in 2.0](sources/whats-new-2.md) — OAS, explicit types, Jobs 2.0, semantic matching
- [What's New in 2.1](sources/whats-new-21.md) — PostgreSQL, diagram enhancements, singleton tables
- [What's New in 2.2](sources/whats-new-22.md) — dj.Instance, thread-safe mode, graph-driven diagrams

### Migration
- [Migrate to DataJoint 2.0](sources/migrate-to-v20.md) — 4-phase migration guide

### Community
- [DataJoint History](sources/history.md) — origin story, Baylor, Vathes, DataJoint Inc.
- [Contributing](sources/contributing.md) — RFC process, testing, PR workflow

## Entities
- [Schema](entities/Schema.md) — dj.Schema object that registers tables in the database
- [dj.Instance](entities/dj.Instance.md) — isolated Config+Connection pair for multi-tenant use

## Concepts
- [Table Tiers](concepts/TableTiers.md) — Manual, Lookup, Imported, Computed, Part
- [Query Algebra](concepts/QueryAlgebra.md) — 7 operators, algebraic closure, dj.Top, dj.U
- [Computation Model](concepts/ComputationModel.md) — make/populate, key_source, three-part make
- [Object Storage (OAS)](concepts/ObjectStorage.md) — inline, hash-addressed, schema-addressed storage
- [Primary Keys](concepts/PrimaryKeys.md) — natural keys, composite keys, dimensions, lineage
- [Semantic Matching](concepts/SemanticMatching.md) — homologous attributes, lineage-based join validation
- [Jobs System](concepts/JobsSystem.md) — Jobs 2.0, per-table ~~table_name, job states
- [Type System](concepts/TypeSystem.md) — three-layer architecture, core types, codec types
- [Entity Integrity](concepts/EntityIntegrity.md) — three questions, dimensions, lineage rules
- [Definition Syntax](concepts/DefinitionSyntax.md) — grammar, types, defaults, indexes
- [Normalization](concepts/Normalization.md) — workflow normalization, intrinsic attributes
- [Relational Workflow Model](concepts/RelationalWorkflowModel.md) — Codd + Chen + workflow

## Syntheses
- [Should a Part Table Be Used as a Foreign Key?](syntheses/part-table-as-fk.md) — when and how to reference Part tables from downstream tables
- [How to Add New Keys to an Existing Table](syntheses/add-new-keys-to-table.md) — alter() limits, migration pattern, and PK design advice
