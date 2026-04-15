---
title: "Relational Workflow Model"
type: concept
tags: [core-concept, architecture, theory]
sources: [relational-workflow-model, faq, data-pipelines, normalization]
last_updated: 2026-04-15
---

# Relational Workflow Model

DataJoint's theoretical foundation, synthesizing three paradigms: Codd's mathematical relational model, Chen's Entity-Relationship model, and modern workflow automation systems. A DataJoint table is simultaneously a mathematical relation, an ER entity type, and a workflow step.

## Three Paradigms

### 1. Codd's Relational Model (Mathematical Foundation)

- Tables as mathematical sets of tuples
- Operations are algebraic (closed: results are also tables)
- Query language is declarative (what, not how)
- DataJoint inherits: 7-operator algebra, algebraic closure, query composability

### 2. Chen's ER Model (Semantic Foundation)

- Tables represent real-world entity types
- Relationships encoded as foreign keys
- Attributes describe entity properties
- DataJoint adds: tier semantics (Manual/Lookup/Imported/Computed), dimension tracking

### 3. Workflow Model (Execution Foundation)

- Tables represent steps in a computation graph
- Dependency order determines execution order
- Completion is binary: a key is either computed or not
- DataJoint adds: `populate()` automation, job coordination, distributed execution

## The Unified View

```
Table = Entity Set ∩ Workflow Step

A row existing = a workflow step completed for that entity
A row missing = work remaining

populate() = find missing rows, call make() for each
```

## Active Schema

The DataJoint schema is "active" — it is simultaneously a data store and a workflow specification. Adding a `Computed` table to the schema is adding a workflow step that will be executed lazily on `populate()`.

## Connections

- [[QueryAlgebra]] — the algebraic component
- [[TableTiers]] — the ER semantic component
- [[ComputationModel]] — the workflow execution component
- [[Normalization]] — workflow normalization principle
