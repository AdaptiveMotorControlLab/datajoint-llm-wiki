---
title: "Query Algebra"
type: concept
tags: [core-concept, query, algebra]
sources: [query-algebra, operators, semantic-matching, relational-workflow-model, faq]
last_updated: 2026-04-15
---

# Query Algebra

DataJoint's query algebra is a mathematically complete, closed set of operations on entity sets. All seven operators return new entity sets — enabling compositional query pipelines. The algebra extends Codd's relational algebra with workflow-specific operators (aggregation, extension) and safe join semantics (semantic matching).

## The Seven Operators

| Operator | Syntax | Description |
|----------|--------|-------------|
| Restriction | `A & cond` | Filter rows matching condition |
| Anti-restriction | `A - cond` | Filter rows NOT matching condition |
| Projection | `A.proj(...)` | Select/rename/compute attributes |
| Join | `A * B` | Combine on shared homologous attributes |
| Extension | `A.extend(B)` | Add optional attributes from B |
| Aggregation | `A.aggr(B, ...)` | Group B by A's PK, compute summaries |
| Union | `A + B` | Combine entity sets with same PK |

## Algebraic Closure

Every operator takes entity sets and returns an entity set — the algebra is closed. This means any query result can be further queried, joined, restricted, or projected.

```python
# All valid — results can be used as inputs to further operators
result = (Subject & "sex='M'") * Session
result2 = result.aggr(Trial, n='count(*)')
subset = result2 & "n > 10"
```

## Special Forms

- `dj.Top(limit, order_by, offset)` — top-N query with ordering/pagination
- `dj.U('attr')` — universal set for ad-hoc grouping/unique extraction
- `dj.U().aggr(Table, ...)` — global (single-row) aggregation

## Key Properties

- **Immutable**: operators never modify originals — all results are new query expressions
- **Composable**: results are entity sets; compose arbitrarily
- **Type-safe**: semantic matching prevents accidental joins

## Connections

- [[operators]] — complete operator reference with examples
- [[SemanticMatching]] — the join validation system
- [[RelationalWorkflowModel]] — theoretical foundation
- [[fetch-results]] — retrieving results from query expressions
