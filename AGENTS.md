# DataJoint Coding Assistant — Agent Instructions

This project uses a DataJoint LLM wiki at `wiki/` containing 54 source pages, 12 concept pages,
and growing syntheses. When working on DataJoint schema design, queries, `make()` methods, or
pipeline architecture, consult the wiki rather than relying solely on general knowledge.

---

## Core DataJoint Concepts

Answer questions in this table immediately without reading the wiki:

| Topic | Core fact |
|-------|-----------|
| **Table tiers** | `Manual` (user-entered, no upstream deps), `Lookup` (small stable reference), `Imported` (populated from external source), `Computed` (derived from upstream via `make()`), `Part` (sub-entities owned by a master table) |
| **Definition syntax** | Lines above `---` are primary key attributes. Lines below `---` are secondary attributes. `->` declares a foreign key and inherits the upstream table's full PK. `<type>` declares a codec/object storage type. |
| **Entity integrity** | `Computed` and `Imported` tables cannot introduce new PK dimensions — every PK attribute must come from FK inheritance. `Part` tables are the only tier allowed to introduce new dimensions (e.g. `cell_id`, `roi_idx`). |
| **populate() / make()** | `populate()` calls `make(key)` for each key in `key_source` not yet present in the table. `key_source` defaults to the natural join of all upstream FK tables minus already-computed rows. |
| **Query algebra** | Restriction `&` / `-`, join `*`, projection `.proj()`, aggregation `.aggr()`, union `+`, universal set `dj.U()`, top-N `dj.Top` |
| **alter()** | Can add, drop, rename, or retype secondary attributes (below `---`). Cannot touch primary keys, foreign keys, or indexes — those require a full table migration. |
| **Primary key rules** | PKs are immutable and define entity identity. Every secondary attribute must be functionally determined by the full PK. Prefer natural keys; use surrogate IDs only when no natural identity exists. |
| **Cascade delete** | Deleting a row cascades automatically to all downstream dependent tables. `dj.config['safemode'] = True` prompts before executing. |

**Triage rule:** If the question is fully answered by the table above, respond directly with a
code example. Only read the wiki for nuanced design trade-offs, specific API options, config
details, error resolution, object storage, migration patterns, or anything beyond the fundamentals.

---

## Wiki-Reading Instructions

When the question requires more than the fundamentals above:

1. Read `wiki/index.md` to identify which Source, Concept, and Synthesis pages are relevant.
2. Check `wiki/syntheses/` first — if an existing synthesis already answers the question, cite
   it directly rather than re-synthesising.
3. Read up to 8 pages, prioritising:
   - `wiki/concepts/` — deeper design rules and mental models
   - `wiki/sources/` — specific API details, how-to patterns, config options
   - `wiki/syntheses/` — prior saved Q&A on related topics
4. Cite sources inline using `[[PageName]]` wikilink notation.
5. Produce working, idiomatic DataJoint code examples grounded in what the wiki says.

---

## Growing the Wiki

If an answer required reasoning not already captured in an existing synthesis, prompt the user:

> This answer isn't in the wiki yet. To save it, create `wiki/syntheses/<slug>.md` using the
> synthesis format in CLAUDE.md, add it to `wiki/index.md` under `## Syntheses`, and append
> `## [YYYY-MM-DD] query | <Title>` to `wiki/log.md`. Then PR it back upstream:
>
> ```bash
> git add wiki/syntheses/ wiki/log.md wiki/index.md
> git commit -m "synthesis: <title>"
> git push && gh pr create
> ```

---

## Directory Layout

```
wiki/
  index.md       # Start here — catalog of all pages
  overview.md    # Living synthesis across all sources
  sources/       # 54 source pages (how-tos, reference, concepts, changelogs)
  concepts/      # 12 concept pages (deep dives: TableTiers, QueryAlgebra, etc.)
  entities/      # Entity pages (Schema, dj.Instance)
  syntheses/     # Saved Q&A — grows with use
```
