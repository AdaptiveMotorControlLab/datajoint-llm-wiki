---
title: "Semantic Matching"
type: source
tags: [conceptual, query-algebra, joins]
date: 2026-04-15
source_file: raw/semantic-matching.md
---

## Summary

Semantic matching is DataJoint's approach to safe automatic joins. Unlike SQL's natural join (which matches any same-named columns), DataJoint only joins on "homologous" attributes — attributes with the same name AND same foreign key lineage. This prevents accidental joins when two tables happen to share a column name (like `id` or `name`) that represents different real-world entities.

## Key Claims

- Semantic matching = name match + lineage match (both required for join)
- Lineage is established through foreign key declarations — `-> ParentTable` propagates parent's PK lineage to child
- If two tables both have `name` but from different origins, joining them raises `NonHomologousError` (not a silent wrong result)
- The fix is always to rename with `.proj()`: `Table.proj(..., unique_name='name')`
- "Namesake" attributes: same name, same lineage — these join correctly
- Lineage assignment rules: original table introduces lineage; FK inheritance propagates it

## Key Quotes

> "SQL's `NATURAL JOIN` silently joins on any shared column name — a hidden bug source. DataJoint's semantic matching raises an error if names match but lineages differ."

> "A homologous attribute is one that can be traced back to the same original table through the foreign key graph."

## Connections

- [[QueryAlgebra]] — semantic matching is part of the join operator definition
- [[operators]] — how semantic matching affects join (*) and restriction (&)
- [[EntityIntegrity]] — attribute lineage as part of data integrity
- [[migrate-to-v20]] — semantic matching introduced in 2.0, may break existing joins

## Contradictions

None identified.
