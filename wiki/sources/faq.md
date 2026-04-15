---
title: "Frequently Asked Questions"
type: source
tags: [conceptual, faq, architecture]
date: 2026-04-15
source_file: raw/faq.md
---

## Summary

FAQ covering DataJoint's design decisions and comparisons. Why a custom definition language instead of SQL/ORM? DataJoint's language encodes both schema and workflow constraints that SQL cannot express (dimension tracking, tier semantics, semantic matching). How does DataJoint compare to workflow managers (Airflow/Prefect), ORMs (SQLAlchemy), and lakehouses?

## Key Claims

- DataJoint's definition language is NOT a simplified ORM — it enforces workflow semantics (dependency tracking, tier classification) that SQL/ORMs don't
- Semantic matching prevents accidental joins on unrelated attributes with the same name — SQL natural join has this bug
- DataJoint vs workflow managers (Airflow): DataJoint tracks data state, not task state — the database IS the scheduler
- DataJoint vs ORMs (SQLAlchemy): ORMs map existing schemas; DataJoint generates schemas from pipeline logic
- DataJoint vs lakehouses: DataJoint optimizes for iterative scientific workflows with strong lineage, not batch analytics
- Composite FK comparison: `(a, b) IN (SELECT a, b FROM Parent)` is how DataJoint's FK semantics translate to SQL

## Key Quotes

> "DataJoint doesn't compete with Airflow — it replaces the need for a separate scheduler by making the database the source of truth for what has been computed and what hasn't."

> "Semantic matching solves the natural join ambiguity problem. SQL's `NATURAL JOIN` will silently join on any same-named column — DataJoint only joins on attributes with shared lineage."

## Connections

- [[SemanticMatching]] — the lineage-based join system
- [[QueryAlgebra]] — DataJoint's query algebra vs SQL
- [[RelationalWorkflowModel]] — theoretical foundation
- [[ComputationModel]] — make/populate vs workflow task systems

## Contradictions

None identified.
