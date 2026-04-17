---
title: "fetch vs to_dicts — choosing a fetch method"
type: synthesis
tags: [fetch, api, query, performance]
sources: [fetch-results, manage-large-data, migrate-to-v20]
last_updated: 2026-04-17
---

## fetch vs to_dicts — choosing a fetch method

These are two distinct approaches to materializing query results, differing in return type, performance profile, and typical use case.

### `to_dicts()` — list of row dicts

```python
rows = (Session & {'mouse_id': 1}).to_dicts()
# [{'mouse_id': 1, 'session_idx': 0, 'session_date': date(2024,1,1), ...}, ...]
```

Returns a `list[dict]`, one dict per row. Per [[fetch-results]], this is the **universal fallback format** — works with every downstream library without any optional dependencies.

Use when:
- You need row-by-row iteration with named fields
- You're feeding data into code that expects dicts (e.g. `insert()` on another table, JSON APIs)
- You don't want a dependency on pandas/polars/pyarrow

**Caveat from [[manage-large-data]]:** for large result sets, `to_dicts()` materializes everything into RAM at once. Prefer the streaming iteration context manager when the result set is large.

### `fetch()` → `to_arrays()` in 2.x

In DataJoint 0.14, `.fetch('attr1', 'attr2')` returned one numpy array per named attribute:

```python
mouse_ids, dates = Session.fetch('mouse_id', 'session_date')
```

In DataJoint 2.x the explicit equivalent is `to_arrays()`:

```python
result = Session.to_arrays()
# {'mouse_id': array([1, 2, ...]), 'session_date': array([...])}
```

Per [[migrate-to-v20]], the 2.0 migration includes an API changes table covering renamed/removed methods — `.fetch()` with attribute arguments maps to `to_arrays()` in the new API.

### Full fetch-method decision guide

| Method | Returns | Use when |
|--------|---------|----------|
| `to_dicts()` | `list[dict]` | General purpose, row-by-row, JSON-compatible |
| `to_pandas()` | `pd.DataFrame` | Analysis, plotting, `.groupby()` |
| `to_polars()` | `pl.DataFrame` | Fast columnar ops (requires polars dep) |
| `to_arrow()` | `pyarrow.Table` | Arrow/Parquet interop |
| `to_arrays()` | `dict[str, np.ndarray]` | Per-attribute numpy arrays |
| `fetch1()` | single `dict` | Exactly one row expected; raises on 0 or 2+ |
| `keys()` | `list[dict]` (PK only) | Avoid fetching secondary attributes |
| streaming iter | lazy rows | Large result sets to avoid RAM exhaustion |

### `fetch1()` — the important special case

```python
# Raises DataJointError if 0 or 2+ rows match — catches cardinality bugs early
row = (Session & {'mouse_id': 1, 'session_idx': 0}).fetch1()
```

`fetch1()` is the right choice **whenever your logic assumes exactly one result**. Using `to_dicts()[0]` silently succeeds even when 2 rows exist, hiding a logic error. `fetch1()` turns that into an explicit exception.

### Large-data note

If a row contains `<npy@>` or `<blob@>` columns, fetching with `to_dicts()` or `to_arrays()` downloads all external objects eagerly. For a 2 GB array, fetch the `NpyRef` first and call `.load(mmap_mode='r')` for lazy slice access — see [[use-npy-codec]].

---

## Sources

- [[fetch-results]] — primary API reference for all fetch methods
- [[manage-large-data]] — streaming vs eager fetch, memory mapping
- [[migrate-to-v20]] — 0.14 → 2.x API method renames
