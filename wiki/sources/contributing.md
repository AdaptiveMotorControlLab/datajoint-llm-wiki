---
title: "Contributing to DataJoint"
type: source
tags: [community, development, contributing]
date: 2026-04-15
source_file: raw/contributing.md
---

## Summary

Guidelines for contributing to DataJoint. Major changes go through an RFC (Request for Comments) process via GitHub issues. Code contributions use standard GitHub fork/PR workflow. Testing uses `pixi` for environment management and `testcontainers` for spinning up real MySQL/PostgreSQL instances in CI.

## Key Claims

- Breaking changes and new features require an RFC issue opened on GitHub before implementation
- Tests must use real databases (via testcontainers), not mocks
- `pixi` manages the development environment and task runner
- All PRs require tests covering the new functionality
- Code style follows the existing project conventions (no specific linter mentioned)

## Key Quotes

> "Open an RFC issue before implementing major changes — get feedback before investing in code."

## Connections

- DataJoint Inc. — organization that reviews RFCs and merges PRs
- [[testing]] — testing philosophy and patterns

## Contradictions

None identified.
