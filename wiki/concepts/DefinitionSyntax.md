---
title: "Definition Syntax"
type: concept
tags: [core-concept, schema-definition, reference]
sources: [definition-syntax, define-tables, design-primary-keys]
last_updated: 2026-04-15
---

# Definition Syntax

DataJoint uses a declarative mini-language for table definitions. The `definition` string is the central artifact of schema design — it specifies the primary key, secondary attributes, foreign key references, and inline documentation.

## Structure

```
# Table comment (optional)
pk_attr1 : type    # inline comment
pk_attr2 : type
---
sec_attr1 : type
sec_attr2 = default : type
-> ForeignTable
```

## Foreign Keys

```python
-> ParentTable                    # inherit all PK attributes
-> ParentTable.proj(new='old')    # rename during inheritance
-> [nullable] ParentTable         # optional reference (secondary only)
-> [unique] ParentTable           # one-to-one constraint
-> [nullable, unique] ParentTable # optional one-to-one
```

## Type Examples

```python
# Core types
mouse_id : varchar(20)
weight : float64
session_date : date
is_active : bool
data : json

# Codec types
image : <blob>          # Python object in DB
large : <blob@>         # Python object external (hash-addressed)
scan : <npy@>           # Numpy array external (schema-addressed)
folder : <object@>      # Zarr group external
file : <filepath@raw>   # Reference to existing file
```

## Defaults

```python
status = "pending" : varchar(20)  # string default
count = 0 : int32                 # numeric default
notes = '' : varchar(1000)        # empty string (preferred for optional strings)
created = CURRENT_TIMESTAMP : datetime
ratio = NULL : float64            # nullable (only NULL can be default)
```

**Key rule**: An attribute is nullable **iff** its default is `NULL`. No other pattern for nullable attributes is allowed.

## Indexes

```python
# In secondary section:
INDEX (session_date)           # single-column
INDEX (subject_id, scan_idx)   # composite
UNIQUE INDEX (email)           # unique constraint
```

## Connections

- [[definition-syntax]] — formal grammar source page
- [[define-tables]] — practical usage guide
- [[TypeSystem]] — all available types
- [[PrimaryKeys]] — primary key design
