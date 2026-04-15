---
title: "Object Storage (OAS)"
type: concept
tags: [core-concept, storage, oas]
sources: [object-storage-overview, use-object-storage, choose-storage-type, configure-storage, garbage-collection, use-npy-codec, manage-large-data, whats-new-2]
last_updated: 2026-04-15
---

# Object Storage (Object-Augmented Schema)

DataJoint's Object-Augmented Schema (OAS) treats large objects as first-class citizens of the schema — not external blobs with database text pointers, but typed schema attributes that DataJoint manages end-to-end. Introduced in DataJoint 2.0.

## Three Storage Modes

### 1. Inline Storage (in the database)

| Type | Use Case |
|------|----------|
| `<blob>` | Small arbitrary Python objects (< ~1MB) |
| `<attach>` | Small file attachments |

### 2. Hash-Addressed External

Content-deduplicating: same bytes → same file on disk, regardless of which row it came from.

| Type | Use Case |
|------|----------|
| `<blob@>` | Large Python objects, numpy arrays (no lazy access needed) |
| `<attach@>` | Large file attachments |

Path: `{location}/_hash/{schema}/{first2}/{rest}.ext`

### 3. Schema-Addressed External

Human-browsable paths keyed by the row's primary key.

| Type | Use Case |
|------|----------|
| `<npy@>` | Numpy arrays (lazy access, metadata inspection) |
| `<object@>` | Zarr groups, folder-structured data |
| `<filepath@>` | User-managed file references (DataJoint stores path only) |

Path: `{location}/_schema/{partition}/{schema}/{table}/{key}/{field}.{token}.{ext}`

## Key Operations

```python
# Insert large array
table.insert1({**key, 'data': my_array})  # automatic encoding

# Fetch reference (no download)
ref = (table & key).fetch1('data')  # returns NpyRef or ObjectRef

# Lazy load
arr = ref.load(mmap_mode='r')  # memory-mapped numpy array

# Stream
with ref.open() as f:
    data = f.read()
```

## Lifecycle Management

- Orphaned objects (row deleted, insert failed) detected by `dj.gc.scan()`
- `dj.gc.collect(dry_run=False)` removes orphans
- Deduplication is per-schema for hash-addressed storage

## Connections

- [[choose-storage-type]] — decision guide
- [[configure-storage]] — store configuration
- [[garbage-collection]] — cleanup
- [[TypeSystem]] — codec types that use OAS
