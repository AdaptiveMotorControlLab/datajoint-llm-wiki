---
title: "Testing DataJoint Pipelines"
type: source
tags: [how-to, testing, best-practices]
date: 2026-04-15
source_file: raw/testing.md
---

## Summary

DataJoint pipelines should be tested against real databases (not mocks). Use `testcontainers` to spin up MySQL/PostgreSQL in CI. Recommended pytest fixture pattern: session-scoped connection, function-scoped schema with UUID names for isolation, auto-cleanup after each test. GitHub Actions CI configuration provided.

## Key Claims

- Use real databases in tests — mocking the DB layer masks real bugs (FK constraints, type coercions, query semantics)
- `testcontainers` library spins up Docker containers for MySQL/PostgreSQL in CI without external DB infrastructure
- Session-scoped connection fixture: create once per test session, reuse across tests
- Function-scoped schema fixture: create unique schema per test (UUID suffix), drop after test — ensures isolation
- `conftest.py` pattern: `@pytest.fixture(scope="session")` for connection, `@pytest.fixture(scope="function")` for schema
- Test blob storage: use temp directory with `protocol: "file"` store in test config

## Key Quotes

> "Don't mock the database. DataJoint's correctness depends on real SQL semantics — foreign key constraints, type coercions, and query algebra all need a real database to test correctly."

## Connections

- [[configuration]] — test configuration patterns
- [[contributing]] — testing requirements for PRs
- [[Schema]] — the schema fixture pattern

## Contradictions

None identified.
