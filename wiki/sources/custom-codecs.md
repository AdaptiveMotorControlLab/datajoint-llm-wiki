---
title: "Custom Codecs"
type: source
tags: [conceptual, codecs, extension, type-system]
date: 2026-04-15
source_file: raw/custom-codecs.md
---

## Summary

Codecs extend DataJoint's type system to support domain-specific data types. Built-in codecs include `<blob>`, `<attach>`, `<npy@>`, and `<object@>`. Custom codecs can encode any Python object (NetworkX graphs, BAM files, SimpleITK images) to a DataJoint-native storage type. Multiple codecs can be chained. Plugin codecs are distributed as packages.

## Key Claims

- Codecs transform between Python objects and DataJoint's core storage types
- `<blob>` codec serializes arbitrary Python objects using pickle-compatible format (mYm/dj0 headers)
- The codec contract: `encode(value)` → storage bytes, `decode(data)` → original Python type
- Chaining codecs (e.g., compress then encrypt) is supported
- Plugin codecs use Python package entry points for auto-registration (`datajoint.codecs` group)
- Available plugin packages: `dj-zarr-codecs`, `dj-figpack-codecs`, `dj-photon-codecs`

## Key Quotes

> "The codec is the bridge between your domain objects and DataJoint's storage layer. It should be stateless and symmetric."

## Connections

- [[TypeSystem]] — three-layer architecture: native → core → codec
- [[create-custom-codec]] — step-by-step guide
- [[use-plugin-codecs]] — using and distributing codec packages
- [[ObjectStorage]] — how codecs relate to external storage

## Contradictions

None identified.
