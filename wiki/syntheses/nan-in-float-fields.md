---
title: "How to store undefined float metrics — the NaN → NULL trap"
type: synthesis
tags: [computed-tables, make, float, nullable, MissingAttributeError, best-practices]
sources: [definition-syntax, type-system, errors, insert-data]
last_updated: 2026-04-17
---

## How to store undefined float metrics — the NaN → NULL trap

### The problem

When a `make()` method produces a float metric that has no meaningful value for a given
key — mean over an empty set, angle between zero-length vectors, speed when a timestamp
lookup fails, a DLC-dependent metric for a session that has no DLC — a natural reflex is
to use Python's `float("nan")` as a sentinel.

**This silently breaks at insert time.** MySQL's `FLOAT` type has no NaN representation.
The pymysql driver converts `float("nan")` to `NULL` before sending the query.  DataJoint
then omits the `NULL` value from the `INSERT` argument list (it strips `None` entries).
MySQL receives an `INSERT` with the column missing entirely and complains:

```
MissingAttributeError: Field 'my_float_field' doesn't have a default value
```

The error message points at a "missing default", not at NaN — the root cause is hidden.
See [[errors]] for the exception hierarchy; `MissingAttributeError` is raised whenever a
required (non-nullable, no-default) column is absent from an `INSERT`.

### The fix — two parts

#### 1. Mark nullable in the table definition

Per [[definition-syntax]]: *"An attribute is nullable if and only if its default is `NULL`."*

For any `float32`/`float64` secondary attribute that can be undefined, write:

```python
class MyAnalysis(dj.Computed):
    definition = """
    -> UpstreamTable
    ---
    # always-present metric — bare type is fine
    path_length: float32

    # may be undefined — mark nullable
    direction_change_rad=NULL: float32    # NULL when window is empty or zero-velocity
    mean_displacement=NULL: float32       # NULL when no events found in session
    push_alignment_mean=NULL: float32     # NULL when DLC unavailable
    """
```

The `=NULL` default tells DataJoint (and MySQL) the column accepts `NULL`, so omitting
the value in an `INSERT` stores `NULL` rather than raising an error.

#### 2. Convert NaN → None before inserting

DataJoint treats Python `None` as SQL `NULL`.  Never pass `float("nan")` — add a small
helper and run every potentially-undefined float through it:

```python
import math

def _nan_to_none(v):
    """Convert float NaN to None for DataJoint nullable fields."""
    if v is None:
        return None
    try:
        return None if math.isnan(v) else v
    except (TypeError, ValueError):
        return v
```

Use it in `make()` when building the row dict:

```python
def make(self, key):
    ...
    direction_change = compute_direction_change(...)   # returns float or nan
    alignment = compute_alignment(...)                 # returns float or nan

    self.insert1({
        **key,
        "path_length": path_length,                          # always valid — no wrapper needed
        "direction_change_rad": _nan_to_none(direction_change),
        "push_alignment_mean": _nan_to_none(alignment["mean"]),
        "mean_displacement": mean_disp if events else None,  # explicit None also fine
    })
```

### When to apply

Apply `=NULL: float32` + `_nan_to_none()` to any secondary float that can be undefined:

| Scenario | Example field |
|---|---|
| Aggregate over empty set | `mean_puck_displacement` when session has no interactions |
| Geometric metric needing two vectors | `puck_direction_change_rad` when pre/post window is empty |
| Metric requiring an optional input | `push_alignment_mean` when DLC state is unavailable |
| Lookup that can miss | `lag_from_initiation_s` when a step is absent from `step_arr` |
| Goal-directed metric without goal | `goal_progress` when trial has no goal (already nullable by convention) |

Fields that are **always computable** given that the row is being inserted at all (e.g. a
count, a bool, a timestamp derived from a guaranteed FK) do not need `=NULL`.

### What not to do

```python
# BAD — pymysql sends nan as NULL, MySQL rejects NOT NULL column with no default
self.insert1({**key, "my_metric": float("nan")})

# BAD — same result; 0.0 is semantically wrong for "undefined"
self.insert1({**key, "my_metric": 0.0})
```

### Summary

| Layer | Rule |
|---|---|
| Table definition | `field=NULL: float32` for any metric that can be undefined |
| Python fallback | Return `None` (not `float("nan")`) when data is unavailable |
| Utility return values | Pass through `_nan_to_none()` before inserting |
| Query side | Filter with `& "my_metric IS NOT NULL"` to exclude undefined rows |

---

## Sources

- [[definition-syntax]] — confirmed `=NULL` is the only way to make a field nullable
- [[type-system]] — `float32`/`float64` are core SQL types; no NaN in MySQL FLOAT
- [[errors]] — `MissingAttributeError` is raised when a required column is absent from INSERT
- [[insert-data]] — DataJoint strips `None` values from INSERT arg lists
