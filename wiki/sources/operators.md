---
title: "Query Operators Reference"
type: source
tags: [reference, api, query-algebra]
date: 2026-04-15
source_file: raw/operators.md
---

## Summary

Complete reference for DataJoint's seven query operators. Restriction (`&`), Anti-restriction (`-`), Projection (`.proj()`), Join (`*`), Extension (`.extend()`), Aggregation (`.aggr()`), Union (`+`). Also covers `dj.Top()` for top-N queries with ordering/pagination, `dj.U()` for universal sets and ad-hoc aggregation, and operator precedence rules.

## Key Claims

- All operators return new query expressions (immutable, composable) — no modification of originals
- Restriction chains combine with AND: `Table & cond1 & cond2`
- `dj.Top(limit, order_by, offset)`: limit=1 default; order_by="KEY" (expands to all PK attrs); `dj.Top` cannot be used with `-`
- Join (`*`) uses semantic matching — only joins on attributes with the same name AND same lineage
- `.aggr(B, ...)` keeps all rows from grouping table by default; use `exclude_nonmatching=True` for inner-join behavior
- `dj.U()` creates ad-hoc groupings or extracts unique values not in the primary key
- `dj.U().aggr(Table, total='count(*)')` computes global (single-row) aggregates
- Operator precedence: `*` > `+`/`-` > `&` (use parentheses to clarify)

## Key Quotes

> "`dj.U('session_date').aggr(Session, n='count(*)')` — aggregate by an attribute that isn't in the primary key using Universal Set."

## Connections

- [[QueryAlgebra]] — formal specification and theory
- [[SemanticMatching]] — how join attributes are validated
- [[fetch-results]] — retrieving results from query expressions
- [[query-data]] — quick reference card

## Contradictions

None identified.
