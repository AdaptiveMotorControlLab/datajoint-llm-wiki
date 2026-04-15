---
title: "Type System"
type: source
tags: [reference, type-system, api]
date: 2026-04-15
source_file: raw/type-system.md
---

## Summary

DataJoint's three-layer type architecture: native Python types → core SQL types → codec types. Core types include int8/16/32/64, float32/64, varchar(n), bool, date, datetime, json, uuid, bytes. Codec types use angle-bracket syntax (`<blob>`, `<npy@>`, `<object@>`). Built-in codecs handle blob serialization (mYm/dj0 format with zlib compression). Plugin codecs extend the system.

## Key Claims

- Three layers: (1) native Python objects, (2) core SQL types (stored in DB), (3) codec types (transform 1↔2)
- Core types: int8/16/32/64, uint8/16/32/64, float32, float64, decimal(p,s), bool, varchar(n), char(n), date, datetime, time, timestamp, json, uuid, bytes (BINARY/VARBINARY)
- Built-in codecs: `<blob>` (pickle-compatible object serialization), `<attach>` (file in DB), `<npy@>` (numpy arrays), `<object@>` (zarr/folder), `<filepath@>` (file reference)
- `<blob>` serialization format: mYm header (MATLAB legacy) or dj0 header + zlib compression + msgpack/pickle
- Plugin codecs in separate packages: `dj-zarr-codecs` (zarr groups), `dj-figpack-codecs` (matplotlib figures), `dj-photon-codecs` (imaging data)
- `enable_python_native_blobs = True` (default): allows any pickleable Python object

## Key Quotes

> "DataJoint's three-layer type system: Python objects at the top, SQL storage at the bottom, codecs as the bridge in between."

## Connections

- [[custom-codecs]] — extending the codec layer
- [[create-custom-codec]] — implementation guide
- [[use-plugin-codecs]] — third-party codec packages
- [[definition-syntax]] — how types appear in table definitions
- [[TypeSystem]] — concept page synthesizing type information

## Contradictions

None identified.
