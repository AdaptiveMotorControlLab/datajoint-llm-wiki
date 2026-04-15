---
title: "Query Algebra Specification"
type: source
tags: [conceptual, reference, query-algebra, formal]
date: 2026-04-15
source_file: raw/query-algebra.md
---

## Summary

Formal specification of DataJoint's query algebra. Defines entity sets (tables with headers), all seven operators with precise semantics, algebraic closure (all operators return entity sets), and how primary key determination works for join results. DataJoint's algebra is a mathematically complete extension of relational algebra tailored for scientific pipelines.

## Key Claims

- All operators produce entity sets (tables with headers) — algebraic closure holds
- Join result's primary key: determined by functional dependencies — if A's PK functionally determines B's PK (or vice versa), the combined PK contracts accordingly
- Restriction is pure filtering — never changes the header or primary key
- Projection can add computed attributes (SQL expressions) in addition to selecting/renaming
- Aggregation is not closed in classical relational algebra but DataJoint makes it closed by requiring the grouping table's PK
- Union requires identical primary keys and non-overlapping secondary attributes
- Extension (`.extend()`) is like a LEFT OUTER JOIN where the right side's PK is a subset of the left

## Key Quotes

> "DataJoint's query algebra is closed: every operation returns an entity set that can be further operated on. This is the mathematical foundation for composable query pipelines."

## Connections

- [[operators]] — practical implementation of the algebra
- [[SemanticMatching]] — the lineage-based attribute matching that enables safe joins
- [[RelationalWorkflowModel]] — broader context for the algebra

## Contradictions

None identified.
