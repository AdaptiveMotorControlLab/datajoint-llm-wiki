---
title: "Table Tiers Diagram"
type: source
tags: [conceptual, table-tiers, visualization]
date: 2026-04-15
source_file: raw/concepts-table-tiers-diagram.md
---

## Summary

A Mermaid flowchart illustrating a complete neuroscience pipeline with color-coded table tiers: Manual (green, mouse metadata/session/scan), Lookup (yellow, parameter sets), Imported (blue, raw data import), Computed (red, automated analysis), and Part (nested subtables). The diagram shows the directed dependency graph from Mouse → Session → Scan → Segmentation → Trace → ReceptiveField.

## Key Claims

- Manual tables (green) are entry points where humans insert data directly
- Lookup tables (yellow) store configuration/parameter sets that rarely change
- Imported tables (blue) pull data from external sources (files, instruments)
- Computed tables (red) run automated algorithms on upstream data
- Part tables appear as nested subtables under their master table (e.g., Segmentation.Cell under Segmentation)
- The color coding matches DataJoint conventions used in ERD visualization

## Key Quotes

> "Each table tier represents a different data entry mode, not just a label — tier determines who populates the table."

## Connections

- [[TableTiers]] — concept page for the tier classification system
- [[ComputationModel]] — how Imported/Computed tables automate processing
- [[EntityIntegrity]] — primary key design shown in the diagram

## Contradictions

None identified.
