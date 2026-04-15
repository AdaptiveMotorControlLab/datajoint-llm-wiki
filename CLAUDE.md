# DataJoint LLM Wiki — Schema & Workflow Instructions

**Domain:** This wiki covers [DataJoint](https://datajoint.com), a Python framework for scientific data pipelines. Sources are the official DataJoint documentation (how-to guides, reference docs, concept explanations, and changelogs). The target audience is researchers and engineers building reproducible data pipelines with DataJoint.

This wiki is maintained entirely by Claude Code. No API key or Python scripts needed — just open this repo in Claude Code and talk to it.

## Slash Commands (Claude Code)

| Command | What to say |
|---|---|
| `/wiki-ingest` | `ingest raw/define-tables.md` |
| `/wiki-query` | `query: how does populate() work?` |
| `/wiki-lint` | `lint the wiki` |
| `/wiki-graph` | `build the knowledge graph` |

Or just describe what you want in plain English:
- *"Ingest this file: raw/query-algebra.md"*
- *"What does the wiki say about the computation model?"*
- *"Check the wiki for orphan pages and contradictions"*
- *"Build the graph and show me what's connected to populate()"*

Claude Code reads this file automatically and follows the workflows below.

---

## Directory Layout

```
raw/          # Immutable DataJoint documentation sources — never modify these
wiki/         # Claude owns this layer entirely
  index.md    # Catalog of all pages — update on every ingest
  log.md      # Append-only chronological record
  overview.md # Living synthesis across all sources
  sources/    # One summary page per source document
  entities/   # DataJoint classes, operators, API methods, and tools
  concepts/   # Core DataJoint ideas, patterns, and frameworks
  syntheses/  # Saved query answers
graph/        # Auto-generated graph data
tools/        # Optional standalone Python scripts (require ANTHROPIC_API_KEY)
```

## DataJoint Domain Context

When ingesting docs and creating entity/concept pages, use these DataJoint-specific categories:

**Entity types (wiki/entities/):**
- Table classes: `Manual`, `Lookup`, `Imported`, `Computed`, `Part`
- Core API: `Schema`, `populate()`, `fetch()`, `insert()`, `delete()`
- Query operators: `Restriction`, `Join`, `Projection`, `Aggregation`, `Union`
- Storage: `Blob`, `Attach`, `Filepath`, `ObjectStorage`, `Codec`
- Tools: `DataJoint CLI`, `Jobs`, `ExternalStore`

**Concept types (wiki/concepts/):**
- `TableTiers` — Manual/Lookup/Imported/Computed hierarchy
- `QueryAlgebra` — restriction, join, projection, aggregation, union
- `ComputationModel` — populate(), make(), job queue, parallelism
- `PrimaryKeys` — design rules, entity integrity
- `ObjectStorage` — blobs, attachments, external stores
- `Normalization` — relational design principles for pipelines
- `EntityIntegrity` — referential integrity, dependency graphs
- `DefinitionSyntax` — table definition DSL syntax rules

---

## Page Format

Every wiki page uses this frontmatter:

```yaml
---
title: "Page Title"
type: source | entity | concept | synthesis
tags: []
sources: []       # list of source slugs that inform this page
last_updated: YYYY-MM-DD
---
```

Use `[[PageName]]` wikilinks to link to other wiki pages.

---

## Ingest Workflow

Triggered by: *"ingest <file>"* or `/wiki-ingest`

Steps (in order):
1. Read the source document fully using the Read tool
2. Read `wiki/index.md` and `wiki/overview.md` for current wiki context
3. Write `wiki/sources/<slug>.md` — use the source page format below
4. Update `wiki/index.md` — add entry under Sources section
5. Update `wiki/overview.md` — revise synthesis if warranted
6. Update/create entity pages for key people, companies, projects mentioned
7. Update/create concept pages for key ideas and frameworks discussed
8. Flag any contradictions with existing wiki content
9. Append to `wiki/log.md`: `## [YYYY-MM-DD] ingest | <Title>`
10. **Post-ingest validation** — check for broken `[[wikilinks]]`, verify all new pages are in `index.md`, print a change summary

### Source Page Format

```markdown
---
title: "Source Title"
type: source
tags: []
date: YYYY-MM-DD
source_file: raw/...
---

## Summary
2–4 sentence summary.

## Key Claims
- Claim 1
- Claim 2

## Key Quotes
> "Quote here" — context

## Connections
- [[EntityName]] — how they relate
- [[ConceptName]] — how it connects

## Contradictions
- Contradicts [[OtherPage]] on: ...
```

### Domain-Specific Templates

Use these DataJoint-specific templates based on the source document type:

#### How-To Guide Template (e.g. fetch-results.md, run-computations.md, insert-data.md)
```markdown
---
title: "How to <Action>"
type: source
tags: [how-to]
date: YYYY-MM-DD
source_file: raw/...
---
## Summary
What this guide covers and when to use it.

## Key Steps / Patterns
- Step/pattern 1
- Step/pattern 2

## Code Examples
Key code snippets from the source.

## Connections
- [[EntityName]] — how they relate
- [[ConceptName]] — relevant concept

## Gotchas / Notes
Important caveats or non-obvious behaviors.

## Contradictions
- Contradicts [[OtherPage]] on: ...
```

#### Reference / API Doc Template (e.g. definition-syntax.md, type-system.md, operators.md)
```markdown
---
title: "Reference: <Topic>"
type: source
tags: [reference]
date: YYYY-MM-DD
source_file: raw/...
---
## Summary
What this reference covers.

## Key Rules / Specifications
- Rule 1
- Rule 2

## Examples
Illustrative examples from the source.

## Connections
- [[ConceptName]] — conceptual context
- [[EntityName]] — related API

## Contradictions
- Contradicts [[OtherPage]] on: ...
```

#### Conceptual Explanation Template (e.g. computation-model.md, query-algebra.md, normalization.md)
```markdown
---
title: "Concept: <Name>"
type: source
tags: [explanation]
date: YYYY-MM-DD
source_file: raw/...
---
## Summary
Core idea in 2–4 sentences.

## Key Claims
- Claim 1
- Claim 2

## Mental Model
How to think about this concept.

## Connections
- [[ConceptName]] — how they relate
- [[EntityName]] — where this manifests in the API

## Contradictions
- Contradicts [[OtherPage]] on: ...
```

#### Changelog / What's New Template (e.g. whats-new-21.md, whats-new-22.md)
```markdown
---
title: "What's New in DataJoint <version>"
type: source
tags: [changelog]
date: YYYY-MM-DD
source_file: raw/...
---
## Summary
Major changes in this version.

## New Features
- Feature 1
- Feature 2

## Breaking Changes / Migration Notes
- Change 1

## Connections
- [[ConceptName]] — features related to this concept
```

---

## Query Workflow

Triggered by: *"query: <question>"* or `/wiki-query`

Steps:
1. Read `wiki/index.md` to identify relevant pages
2. Read those pages with the Read tool
3. Synthesize an answer with inline citations as `[[PageName]]` wikilinks
4. Ask the user if they want the answer filed as `wiki/syntheses/<slug>.md`

---

## Lint Workflow

Triggered by: *"lint the wiki"* or `/wiki-lint`

Use Grep and Read tools to check for:
- **Orphan pages** — wiki pages with no inbound `[[links]]` from other pages
- **Broken links** — `[[WikiLinks]]` pointing to pages that don't exist
- **Contradictions** — claims that conflict across pages
- **Stale summaries** — pages not updated after newer sources
- **Missing entity pages** — entities mentioned in 3+ pages but lacking their own page
- **Data gaps** — questions the wiki can't answer; suggest new sources

Output a lint report and ask if the user wants it saved to `wiki/lint-report.md`.

---

## Graph Workflow

Triggered by: *"build the knowledge graph"* or `/wiki-graph`

When the user asks to build the graph, run `tools/build_graph.py` which:
- Pass 1: Parses all `[[wikilinks]]` → deterministic `EXTRACTED` edges
- Pass 2: Infers implicit relationships → `INFERRED` edges with confidence scores
- Runs Louvain community detection
- Outputs `graph/graph.json` + `graph/graph.html`

If the user doesn't have Python/dependencies set up, instead generate the graph data manually:
1. Use Grep to find all `[[wikilinks]]` across wiki pages
2. Build a node/edge list
3. Write `graph/graph.json` directly
4. Write `graph/graph.html` using the vis.js template

---

## Naming Conventions

- Source slugs: `kebab-case` matching source filename (e.g. `define-tables.md` → slug `define-tables`)
- Entity pages: `TitleCase.md` (e.g. `Schema.md`, `Computed.md`, `populate.md`)
- Concept pages: `TitleCase.md` (e.g. `QueryAlgebra.md`, `ComputationModel.md`, `TableTiers.md`)
- Source pages: `kebab-case.md`

## Index Format

```markdown
# Wiki Index

## Overview
- [Overview](overview.md) — living synthesis

## Sources
- [Source Title](sources/slug.md) — one-line summary

## Entities
- [Entity Name](entities/EntityName.md) — one-line description

## Concepts
- [Concept Name](concepts/ConceptName.md) — one-line description

## Syntheses
- [Analysis Title](syntheses/slug.md) — what question it answers
```

## Log Format

Each entry starts with `## [YYYY-MM-DD] <operation> | <title>` so it's grep-parseable:

```
grep "^## \[" wiki/log.md | tail -10
```

Operations: `ingest`, `query`, `lint`, `graph`
