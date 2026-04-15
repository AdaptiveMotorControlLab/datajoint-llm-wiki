---
title: "Should a Part Table Be Used as a Foreign Key in Another Table?"
type: synthesis
tags: [part-tables, primary-keys, schema-design, fk]
sources: [entity-integrity, define-tables, design-primary-keys, normalization]
last_updated: 2026-04-15
---

## Can a Part Table Be Used as a Foreign Key Reference (in Another Table's Primary Key)?

**Yes — and it's the standard pattern for downstream computations on sub-entities.** But there are important constraints on *how* and *which tier* should do the referencing.

---

### Why Part Tables Exist for This Purpose

[[TableTiers]] establishes the rule: **Computed tables cannot introduce new primary key dimensions** — they can only inherit attributes from upstream foreign keys. Part tables exist precisely to fill this gap. They are the *only* tier allowed to introduce new dimensions within a computed pipeline.

The canonical example from [[EntityIntegrity]]:

```
Segmentation (dj.Computed)   — computes cell segmentations per scan
Segmentation.Cell (dj.Part)  — introduces cell_id (new dimension)
```

`Segmentation.Cell` creates entities (individual neurons/cells) that didn't exist as first-class rows before `Segmentation.make()` ran. A downstream table that wants to compute a property *per cell* must reference it:

```python
@schema
class Trace(dj.Computed):
    definition = """
    -> Segmentation.Cell    # inherits subject_id, session_idx, scan_idx, cell_id
    ---
    trace : <npy@>          # fluorescence trace for this cell
    """

    def make(self, key):
        # key contains the full chain: subject_id, session_idx, scan_idx, cell_id
        ...
        self.insert1({**key, 'trace': fluorescence_array})
```

This is explicitly the intended pattern. `-> Segmentation.Cell` in `Trace`'s primary key gives `Trace` the full inherited key `(subject_id, session_idx, scan_idx, cell_id)`. The lineage chain is intact, [[SemanticMatching]] works correctly, and cascade deletion propagates as expected.

---

### What Tier Should Reference the Part Table?

| Referencing tier | Valid? | Notes |
|------------------|--------|-------|
| `dj.Computed` | ✅ Yes — standard | The downstream table computes properties per sub-entity |
| `dj.Imported` | ✅ Yes | E.g. importing per-cell metadata from an external file |
| `dj.Manual` | ✅ Yes | E.g. manually curating a subset of cells |
| Another `dj.Part` of *a different master* | ⚠️ Avoid | Creates a cross-master dependency that breaks Part semantics |
| `dj.Part` of the *same master* | ✅ Yes | Sub-parts of the same computation are fine |

---

### Key Constraints to Keep in Mind

**1. You inherit the full lineage chain.**
`-> Segmentation.Cell` doesn't just import `cell_id` — it imports *every* attribute in `Segmentation.Cell`'s primary key: `(subject_id, session_idx, scan_idx, cell_id)`. This is correct behaviour per [[PrimaryKeys]]: the downstream table's rows represent "one computation per cell", and the cell's full identity includes its parent scan's identity.

**2. Cascade deletion propagates correctly.**
Deleting a `Segmentation` row cascades through `Segmentation.Cell` (its part) and then further to `Trace` and any other tables referencing it. This is the expected and safe behavior — deleting a segmentation should invalidate all downstream per-cell results.

**3. The Part table must be populated before downstream `populate()` runs.**
`Trace.populate()` will pick up keys from `Segmentation.Cell` as its `key_source`. If `Segmentation.make()` hasn't run yet (and therefore `Segmentation.Cell` is empty), `Trace.populate()` is a no-op. This dependency ordering is automatic and correct.

**4. Do not introduce a new dimension in the referencing `Computed` table.**
Per [[EntityIntegrity]], `Trace` must get *all* its PK attributes from foreign keys — it cannot add its own new dimension. `-> Segmentation.Cell` satisfies this rule since `cell_id` is introduced by the Part table, not by `Trace` itself. If a new dimension is needed at the `Trace` level (e.g., a `time_bin_idx`), use a nested Part table: `Trace.TimeBin(dj.Part)`.

---

### What to Avoid

```python
# ❌ WRONG — cross-master Part dependency
@schema
class BehaviorAnalysis(dj.Computed):
    class BehaviorTrial(dj.Part):  # Part of BehaviorAnalysis
        definition = """
        -> Segmentation.Cell    # Referencing a Part of a different master — confusing ownership
        ...
        """
```

This creates ownership confusion: `BehaviorAnalysis.BehaviorTrial` would be populated by `BehaviorAnalysis.make()`, but it depends on `Segmentation.Cell` which is populated by `Segmentation.make()`. The cleaner design is a plain `Computed` table:

```python
# ✅ CORRECT — standalone Computed table referencing the Part
@schema
class CellBehaviorCorrelation(dj.Computed):
    definition = """
    -> Segmentation.Cell
    -> BehaviorSession
    ---
    correlation : float64
    """
```

---

### Summary

Using a Part table as a foreign key in another table's primary key is **normal, expected, and the intended design pattern** in DataJoint. It is how sub-entities introduced by Part tables get computed on downstream. The rules to follow: the referencing table should be a `Computed`, `Imported`, or `Manual` (not an unrelated master's Part), and it must not introduce additional new dimensions of its own beyond what it inherits.

---

## Sources

- [[EntityIntegrity]] — dimension rules, Part tables can introduce new dimensions
- [[TableTiers]] — Computed tables cannot add dimensions; Part tables can
- [[PrimaryKeys]] — FK inheritance, lineage chain in composite keys
- [[Normalization]] — one table = one workflow step, intrinsic attributes
- [[SemanticMatching]] — lineage propagation through FK chains
