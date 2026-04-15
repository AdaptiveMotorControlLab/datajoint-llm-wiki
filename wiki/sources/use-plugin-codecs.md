---
title: "Use Plugin Codecs"
type: source
tags: [how-to, codecs, extension]
date: 2026-04-15
source_file: raw/use-plugin-codecs.md
---

## Summary

Plugin codecs extend DataJoint's type system via Python package entry points. Install a codec package (e.g., `pip install dj-zarr-codecs`), and the codec auto-registers on import. No configuration needed. Covers how to distribute your own codecs, versioning strategies (codec_version field, decode dispatch by version), and the entry point registration mechanism.

## Key Claims

- Plugin codecs use Python package entry points under the `datajoint.codecs` group
- Installing a codec package makes it available automatically on next DataJoint import
- `dj-zarr-codecs`: `<zarr@>` for zarr array groups
- `dj-figpack-codecs`: `<figure@>` for matplotlib figures
- `dj-photon-codecs`: domain-specific imaging data codecs
- Versioning strategy: add `codec_version : int` to table PK; dispatch in `decode()` by version
- To distribute: add entry point `datajoint.codecs = mypackage.module:MyCodecClass` in `pyproject.toml`

## Key Quotes

> "Plugin codecs are zero-configuration — install the package, import DataJoint, and the codec is available."

## Connections

- [[custom-codecs]] — codec system overview
- [[create-custom-codec]] — implementing a codec
- [[TypeSystem]] — where codecs fit

## Contradictions

None identified.
