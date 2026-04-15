---
title: "Table Definition Syntax"
type: source
tags: [reference, syntax, schema-definition]
date: 2026-04-15
source_file: raw/definition-syntax.md
---

## Summary

Formal reference for DataJoint's declarative table definition language. The `definition` string has a fixed structure: optional table comment, primary key attributes, `---` separator, secondary attributes. Attributes can be core types (int32, float64, varchar), codec types (`<blob>`, `<npy@>`), or have defaults and inline comments. Foreign keys use `->` with optional `[nullable]` and `[unique]` modifiers.

## Key Claims

- Grammar: `[comment] pk_attrs "---" secondary_attrs` where each attr is `[default "="] name ":" type [# comment]`
- Codec types use angle brackets: `<blob>`, `<attach@store>`, `<npy@>`, `<object@>`, `<filepath@raw>`
- Defaults: literal values, `NULL` (makes attribute nullable), `CURRENT_TIMESTAMP`, or empty string `''`
- Nullable iff default is `NULL` — no other pattern allowed; prefer `''` for optional strings
- `[nullable]` and `[unique]` FK modifiers: nullable means FK can be NULL, unique creates UNIQUE INDEX
- `[nullable, unique]` creates optional one-to-one relationship; multiple NULLs allowed (SQL UNIQUE ignores NULLs)
- Validation at declaration time: at least one PK attr, valid identifiers, recognized types, no circular deps

## Key Quotes

> "An attribute is nullable if and only if its default is `NULL`. DataJoint does not allow other defaults for nullable attributes."

## Connections

- [[define-tables]] — practical guide using this syntax
- [[TypeSystem]] — all supported types
- [[PrimaryKeys]] — primary key design
- [[EntityIntegrity]] — how PK design ensures data integrity

## Contradictions

None identified.
