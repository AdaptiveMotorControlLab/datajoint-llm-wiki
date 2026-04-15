---
title: "Fetch Results"
type: source
tags: [reference, api, fetch]
date: 2026-04-15
source_file: raw/fetch-results.md
---

## Summary

Reference for DataJoint's fetch API. Query results can be retrieved as dicts (`to_dicts()`), pandas DataFrame (`to_pandas()`), polars DataFrame (`to_polars()`), PyArrow Table (`to_arrow()`), numpy arrays (`to_arrays()`), or keys only (`keys()`). `fetch1()` asserts exactly one row. All fetch methods accept `order_by`, `limit`, and `offset` parameters.

## Key Claims

- `to_dicts()` returns a list of row dicts — the universal fallback format
- `to_pandas()` returns a DataFrame; `to_polars()` returns a polars DataFrame (requires optional dep)
- `to_arrow()` returns a PyArrow Table for interop with Arrow-based systems
- `to_arrays()` returns a dict of numpy arrays keyed by attribute name
- `fetch1()` fetches exactly one row and raises if 0 or 2+ rows match — use when expecting a unique result
- `keys()` returns only primary key dicts — efficient when secondary attributes aren't needed
- Streaming iteration available for large result sets via lazy iteration context manager
- `order_by`, `limit`, `offset` parameters work on all fetch methods

## Key Quotes

> "`fetch1()` is the right choice when you expect exactly one result. It will raise if your assumption is wrong — catching bugs early."

## Connections

- [[QueryAlgebra]] — queries that produce the results being fetched
- [[operators]] — operators that filter/project before fetching
- [[use-npy-codec]] — fetching NpyRef objects and lazy loading

## Contradictions

None identified.
