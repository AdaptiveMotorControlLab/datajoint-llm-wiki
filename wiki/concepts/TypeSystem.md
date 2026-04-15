---
title: "Type System"
type: concept
tags: [core-concept, types, codecs]
sources: [type-system, custom-codecs, create-custom-codec, use-plugin-codecs, definition-syntax]
last_updated: 2026-04-15
---

# Type System

DataJoint's type system has three layers: native Python types, core SQL types, and codec types. Codecs are the bridge between Python objects and SQL storage.

## Three-Layer Architecture

```
Python Objects (native types)
        ↕  codec encode/decode
Core SQL Types (stored in database)
        ↕  codec types in definition
Codec Types (<blob>, <npy@>, etc.)
```

## Core Types (Layer 2)

| Type | SQL | Notes |
|------|-----|-------|
| `int8` `int16` `int32` `int64` | TINYINT/SMALLINT/INT/BIGINT | |
| `uint8` `uint16` `uint32` | TINYINT/SMALLINT/INT UNSIGNED | |
| `float32` `float64` | FLOAT/DOUBLE | |
| `decimal(p,s)` | DECIMAL(p,s) | Exact numeric |
| `bool` | TINYINT(1) | |
| `varchar(n)` | VARCHAR(n) | Up to 65535 |
| `char(n)` | CHAR(n) | Fixed length |
| `date` | DATE | |
| `datetime` | DATETIME | |
| `json` | JSON | Structured data |
| `uuid` | CHAR(36) | UUID v4 |
| `bytes` | VARBINARY | Raw bytes |

## Built-In Codec Types (Layer 3)

| Codec | Storage | Use Case |
|-------|---------|----------|
| `<blob>` | LONGBLOB | Any Python object (in DB) |
| `<attach>` | LONGBLOB | File content (in DB) |
| `<blob@>` | External hash | Large objects (dedup) |
| `<attach@>` | External hash | Large files (dedup) |
| `<npy@>` | External schema | Numpy arrays (lazy) |
| `<object@>` | External schema | Zarr/folder data |
| `<filepath@>` | VARCHAR | User-managed path |

## Custom Codecs

```python
@dj.codec_type("<mytype>")
class MyCodec(dj.Codec):
    def get_dtype(self):
        return "<blob>"  # underlying storage type
    
    def encode(self, value):
        return serialize(value)
    
    def decode(self, data):
        return deserialize(data)
```

## Plugin Codecs

- `dj-zarr-codecs`: `<zarr@>` for zarr array groups
- `dj-figpack-codecs`: `<figure@>` for matplotlib figures
- `dj-photon-codecs`: imaging domain codecs

## Connections

- [[custom-codecs]] — codec system overview
- [[create-custom-codec]] — implementing custom codecs
- [[use-plugin-codecs]] — third-party codec packages
- [[ObjectStorage]] — how codec types map to external stores
