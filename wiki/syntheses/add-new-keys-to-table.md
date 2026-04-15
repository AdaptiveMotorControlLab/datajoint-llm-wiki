---
title: "How to Add New Keys to an Existing Table"
type: synthesis
tags: [primary-keys, migration, alter, schema-design]
sources: [alter-tables, definition-syntax, design-primary-keys, entity-integrity, deploy-production]
last_updated: 2026-04-15
---

## How Can One Add New Keys to an Existing Table?

The answer depends on what kind of "key" you mean — and in the most important case (primary key), the honest answer is: **DataJoint has no in-place mechanism for it**. It requires a manual migration.

---

### What `alter()` Can and Cannot Do

[[alter-tables]] wraps MySQL's `ALTER TABLE` and supports a specific set of non-destructive changes:

| Change | Supported by `alter()`? |
|--------|------------------------|
| Add a new **secondary** attribute (with default or nullable) | ✅ Yes |
| Drop a secondary attribute | ✅ Yes |
| Modify a secondary attribute's type or default | ✅ Yes |
| Rename a secondary attribute | ✅ Yes |
| Add a **primary key** attribute | ❌ No |
| Add or remove a **foreign key** (`->`) declaration | ❌ No |
| Add or remove an **index** | ❌ No |

The restriction is firm. Primary keys, foreign keys, and indexes are considered part of the table's structural identity — `alter()` cannot touch them.

---

### Adding a Secondary Key Index

If you want to add a new database index (not a PK attribute — just faster lookup), that is also unsupported by `alter()`. You must do a migration (see below), or restructure to use a `UNIQUE INDEX` or `INDEX` at table creation time:

```python
definition = """
-> Subject
session_idx : int16
---
session_date : date
INDEX (session_date)        # add searchable index at definition time
"""
```

---

### Adding a New Primary Key Attribute (the Hard Case)

This is the scenario most people mean by "add new keys." For example, suppose you have:

```python
@schema
class Analysis(dj.Computed):
    definition = """
    -> Session
    ---
    result : float64
    """
```

And you want to add a `-> ParameterSet` foreign key to the primary key (so that results are tracked per session × parameter set). This changes the table's identity — existing rows no longer have unambiguous meaning under the new schema.

**DataJoint has no built-in command for this.** You must do a full migration:

#### Migration Pattern

```python
# Step 1 — Define the new table alongside the old one
@schema
class AnalysisV2(dj.Computed):
    definition = """
    -> Session
    -> ParameterSet           # new PK dimension
    ---
    result : float64
    """
    def make(self, key):
        ...

# Step 2 — Copy existing data (mapping old rows to new PK)
# Option A: recompute from scratch (safest)
AnalysisV2.populate()

# Option B: migrate data directly if recomputation is expensive
rows = Analysis.fetch(as_dict=True)
for row in rows:
    row['param_id'] = DEFAULT_PARAM_ID   # assign default for new key
    AnalysisV2.insert1(row, skip_duplicates=True)

# Step 3 — Verify AnalysisV2 looks correct

# Step 4 — Drop the old table
# (requires deleting rows first if safemode is on)
Analysis.drop()

# Step 5 — Rename AnalysisV2 → Analysis in code
# (update class name, redeploy)
```

Per [[deploy-production]]: **create new tables before dropping old ones** — never drop first, or you lose the data you need to migrate.

---

### Adding a New Foreign Key to the Primary Key: Dimensional Implications

[[EntityIntegrity]] is important here: adding a new FK to the PK means you are adding a new **dimension** to the entity. This has downstream consequences:

- **All downstream tables** that reference `-> Analysis` in their own PK must also be migrated, because the FK chain has changed
- If you have `Trace -> Analysis -> Session`, adding a dimension to `Analysis` means `Trace` must also gain that dimension (either explicitly via `-> ParameterSet` or implicitly by re-referencing `-> AnalysisV2`)
- [[SemanticMatching]] will correctly propagate the new lineage through the FK graph once migration is complete

This is why DataJoint's [[PrimaryKeys]] guidance emphasizes getting PK design right upfront: changing it later is a multi-table migration.

---

### The Practical Decision Tree

```
Want to add a "key" to a table?
│
├─ It's a secondary attribute (below ---)?
│   └─ Use alter(). Done.
│
├─ It's a new primary key attribute or FK in the PK?
│   ├─ Table has no data yet?
│   │   └─ Just update the definition string. DataJoint re-creates on next import.
│   └─ Table has existing data?
│       └─ Full migration: new class → copy data → drop old → rename
│
└─ It's a new index (INDEX / UNIQUE INDEX)?
    └─ Full migration required (alter() doesn't support index changes)
```

---

### Avoiding the Problem in the First Place

[[design-primary-keys]] recommends thinking through the full set of dimensions an entity will ever need *before* first deployment. The two most common mistakes that lead to "I need to add a PK attribute later":

1. **Forgetting a parameter dimension** — you compute results, then realize you need to track which parameter set was used. Fix: add `-> ParameterSet` to the PK from the start, even if you only have one parameter set initially.
2. **Under-specifying the entity** — you track sessions but later realize sessions can have multiple runs. Fix: add `run_idx` to the PK upfront, or use a Part table for the sub-entities.

---

## Sources

- [[alter-tables]] — what `alter()` can and cannot change
- [[DefinitionSyntax]] — primary key structure and FK declarations
- [[design-primary-keys]] — PK design philosophy and common mistakes
- [[EntityIntegrity]] — dimensions, PK immutability, downstream cascade
- [[PrimaryKeys]] — composite keys and lineage
- [[deploy-production]] — safe migration ordering (create before drop)
- [[SemanticMatching]] — how FK lineage propagates through changed PK chains
