# DataJoint Coding Assistant

This repo includes a DataJoint LLM wiki at `wiki/`. For non-trivial schema design, query
writing, or pipeline architecture questions, read `wiki/index.md` to find relevant pages and
consult them before suggesting code.

## DataJoint Fundamentals

- **Table tiers:** `Manual` (user data) | `Lookup` (reference) | `Imported` (external source) | `Computed` (derived via `make()`) | `Part` (sub-entities of a master)
- **Definition syntax:** Above `---` = primary key. Below `---` = secondary attrs. `->` = foreign key (inherits full PK upstream). `<type>` = codec.
- **Entity integrity:** `Computed`/`Imported` cannot add new PK dimensions — only `Part` tables can introduce new dimensions. All PK attrs must come from FK inheritance.
- **populate()/make():** `populate()` calls `make(key)` for each uncomputed key in `key_source` (default: join of all upstream FK tables).
- **Query algebra:** `&`/`-` restrict, `*` join, `.proj()` project, `.aggr()` aggregate, `+` union, `dj.U()` universal set, `dj.Top` limit.
- **alter():** Secondary attrs only. Changing PKs, FKs, or indexes requires a full migration (new class → copy data → drop old).
- **PK rules:** Immutable, define entity identity. Secondary attrs must be functionally determined by full PK. Prefer natural keys.
- **Cascade delete:** Cascades downstream automatically. `safemode=True` prompts first.

## Wiki Structure

```
wiki/index.md     — start here; catalog of all pages
wiki/concepts/    — deep design rules (TableTiers, QueryAlgebra, EntityIntegrity, …)
wiki/sources/     — API details, how-tos, config, changelogs
wiki/syntheses/   — saved Q&A; grows with use
```

For non-obvious answers, suggest the user save the result to `wiki/syntheses/<slug>.md`
following the synthesis format in CLAUDE.md.
