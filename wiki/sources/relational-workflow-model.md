---
title: "Relational Workflow Model"
type: source
tags: [conceptual, architecture, theory]
date: 2026-04-15
source_file: raw/relational-workflow-model.md
---

## Summary

DataJoint's Relational Workflow Model synthesizes three paradigms: Codd's mathematical relational model (algebraic closure, relational completeness), Chen's Entity-Relationship model (semantic table types), and the workflow model (tables as pipeline steps with execution semantics). Each table in a DataJoint pipeline represents both a data entity AND a workflow step — compute and store are unified.

## Key Claims

- Three paradigms merged: mathematical relations (Codd) + ER modeling (Chen) + workflow execution
- A DataJoint table is simultaneously: a relation (supports algebraic operators), an entity type (has semantic meaning), and a workflow step (can be populated automatically)
- "Active schema" concept: the schema itself is the workflow — adding a row to a Computed table is the "completion" of that computation step
- Query algebra inherits from Codd: algebraic closure, 7 fundamental operators
- Workflow normalization principle: one table = one pipeline step
- DataJoint rejects the view/materialized-view distinction — Computed tables ARE materialized views that the framework maintains

## Key Quotes

> "DataJoint unifies three traditions: the mathematics of Codd's relational model, the semantics of Chen's entity-relationship model, and the automation of modern workflow systems."

> "In DataJoint, the schema is the workflow. If a row doesn't exist in a Computed table, the computation hasn't happened yet."

## Connections

- [[QueryAlgebra]] — the algebraic component
- [[TableTiers]] — the ER semantic component (tier classification)
- [[ComputationModel]] — the workflow execution component
- [[Normalization]] — workflow normalization principle

## Contradictions

None identified.
