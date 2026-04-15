---
title: "Use DataJoint CLI"
type: source
tags: [how-to, cli, tools]
date: 2026-04-15
source_file: raw/use-cli.md
---

## Summary

The `dj` command provides an interactive REPL for DataJoint. Connect with `dj --host localhost --user root --password secret`. Load schemas with `-s schema_name:alias` (e.g., `-s my_pipeline:p`). Inside the REPL, tables are accessible as `p.MyTable`, queries work normally, and results display inline.

## Key Claims

- `dj` launches an interactive Python REPL with DataJoint pre-configured
- `--host`, `--user`, `--password` flags for connection (alternatively reads from config)
- `-s schema_name:alias` loads a schema as `alias` in the REPL namespace
- Multiple schemas can be loaded: `dj -s pipeline:p -s analysis:a`
- Tab completion available for table names and attributes
- Useful for quick data inspection, ad-hoc queries, and debugging

## Key Quotes

> "The `dj` REPL is useful for quick data exploration — connect, load your schema, and query interactively without writing a script."

## Connections

- [[configuration]] — config read by CLI
- [[Schema]] — schemas loaded into the REPL

## Contradictions

None identified.
