---
title: "Create Custom Codec"
type: source
tags: [how-to, codecs, extension]
date: 2026-04-15
source_file: raw/create-custom-codec.md
---

## Summary

Step-by-step guide for creating a custom DataJoint codec by subclassing `dj.Codec`. A codec defines `get_dtype()` (returns the storage type), `encode(value)` (serializes to storage format), `decode(data)` (deserializes from storage), and optionally `validate(value)` (validates before insert). Codecs register automatically when the module is imported.

## Key Claims

- Subclass `dj.Codec` and implement `get_dtype()`, `encode()`, `decode()` — `validate()` is optional
- `get_dtype()` returns a DataJoint core type string (e.g., `"<blob>"`, `"varchar(255)"`)
- Codecs auto-register by class name when the module containing them is imported
- Use `@dj.codec_type("<mytype>")` decorator to set the codec's type name
- Codec must handle both serialization and deserialization symmetrically
- Plugin codecs can be distributed as packages using Python entry points

## Key Quotes

> "A codec is just encode + decode + a type name. Keep encode/decode symmetric and stateless."

## Connections

- [[custom-codecs]] — broader overview of the codec system
- [[TypeSystem]] — where codecs fit in the type architecture
- [[use-plugin-codecs]] — distributing codecs as packages

## Contradictions

None identified.
