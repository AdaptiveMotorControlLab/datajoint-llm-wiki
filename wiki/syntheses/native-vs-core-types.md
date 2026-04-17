---
title: "Native Types vs Core DataJoint Types"
type: synthesis
tags: [type-system, schema-design, codecs, storage]
sources: [type-system, definition-syntax, choose-storage-type]
last_updated: 2026-04-16
---

## Native Types vs Core DataJoint Types

DataJoint's [[TypeSystem]] has three distinct layers. The confusion between "native" and "core"
types is real and worth unpacking precisely.

---

### The Three Layers

```
Layer 1 — Python native types (what your code works with)
              ↕  codec encode / decode
Layer 2 — Core SQL types (what gets stored in the database)
              ↕  definition string syntax
Layer 3 — Codec types (angle-bracket types like <blob>, <npy@>)
```

---

### Layer 2 — Core DataJoint Types

These are DataJoint's first-class scalar types. They map directly to SQL column types and require
no codec — what you store is what the database holds:

| DataJoint type | SQL column | When to use |
|---|---|---|
| `int8` `int16` `int32` `int64` | TINYINT / SMALLINT / INT / BIGINT | Integers |
| `uint8` `uint16` `uint32` | Unsigned variants | Non-negative integers |
| `float32` `float64` | FLOAT / DOUBLE | Floating point |
| `decimal(p,s)` | DECIMAL(p,s) | Exact numeric (finance, doses) |
| `bool` | TINYINT(1) | True/False |
| `varchar(n)` | VARCHAR(n) | Strings up to 65535 chars |
| `char(n)` | CHAR(n) | Fixed-length strings |
| `date` | DATE | Calendar date |
| `datetime` | DATETIME | Date + time |
| `json` | JSON | Structured dict/list data |
| `uuid` | CHAR(36) | UUID v4 identifiers |
| `bytes` | VARBINARY | Raw binary |

```python
definition = """
subject_id   : varchar(20)
session_date : date
weight_g     : float32
n_trials     : int32
metadata     : json
---
notes = '' : varchar(1000)
"""
```

Use core types whenever your data is a scalar, a string, or structured JSON. They are queryable,
indexable, and human-readable in the database.

---

### Layer 3 — Codec Types

Codec types use angle-bracket syntax and are needed when you want to store Python objects, arrays,
or files that have no direct SQL equivalent. A codec serialises the Python value for storage and
deserialises on fetch:

| Codec type | Underlying storage | Typical use |
|---|---|---|
| `<blob>` | LONGBLOB (in DB) | Any pickleable Python object, small–medium size |
| `<blob@store>` | External, hash-addressed | Large Python objects, with deduplication |
| `<attach>` | LONGBLOB (in DB) | Small files stored inside the DB |
| `<attach@store>` | External, hash-addressed | Large files, deduplicated |
| `<npy@store>` | External, schema-addressed | NumPy arrays; supports lazy load / mmap |
| `<object@store>` | External, schema-addressed | Zarr groups, hierarchical folder data |
| `<filepath@store>` | VARCHAR (path only) | Reference to user-managed files, not managed by DJ |

```python
definition = """
-> Session
---
trace     : <npy@>       # numpy array — lazy loadable
params    : <blob>        # small Python dict or object
raw_video : <attach@>    # large file, deduplicated externally
report    : <attach>      # small PDF in DB
"""
```

---

### The Key Decision

**Use a core type** when:
- Your value is a scalar, string, date, or JSON-serialisable dict
- You want the value to be queryable/filterable in SQL (e.g., `& 'weight_g > 20'`)
- You care about human-readability in the DB

**Use a codec type** when:
- Your value is a Python object with no native SQL equivalent (array, model, figure)
- Your data is large (>~1 MB) and belongs outside the DB
- You need lazy loading (`<npy@>`), deduplication (`<blob@>`, `<attach@>`), or folder addressing (`<object@>`)

**A common mistake** is using `<blob>` to store something that has a perfectly good core type —
e.g. storing a Python `dict` with `<blob>` when `json` would keep the data queryable. Prefer
core types wherever possible; reach for codecs only when core types can't represent the data.

---

### `enable_python_native_blobs`

`enable_python_native_blobs = True` (default on) allows `<blob>` to serialise any pickleable
Python object. With it off, `<blob>` only accepts objects serialisable via the mYm/dj0 format.
Most users leave it on.

---

## Sources

- [[TypeSystem]] — three-layer architecture, full type tables
- [[type-system]] — source page with serialisation format details
- [[DefinitionSyntax]] — how types appear in definition strings
- [[choose-storage-type]] — decision guide for codec type selection
