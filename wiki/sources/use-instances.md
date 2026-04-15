---
title: "Use dj.Instance"
type: source
tags: [how-to, api, multi-tenant]
date: 2026-04-15
source_file: raw/use-instances.md
---

## Summary

`dj.Instance` provides isolated database connections independent of global `dj.config` and `dj.conn()`. Create with `dj.Instance(host=, user=, password=)`. Use `inst.Schema("name")` instead of `dj.Schema("name")`. Multiple instances can connect to different databases simultaneously. Essential for thread-safe, multi-tenant, and test isolation use cases.

## Key Claims

- `dj.Instance(host, user, password, **kwargs)` creates an isolated config+connection pair
- `inst.Schema("schema_name")` creates a Schema bound to this instance's connection
- `inst.FreeTable("full_table_name")` creates a FreeTable bound to this instance
- Multiple instances can run concurrently with different credentials/databases
- Flask per-request pattern: create a fresh Instance per request for thread safety
- Test fixture pattern: one Instance per test to ensure complete isolation
- In thread-safe mode (`DJ_THREAD_SAFE=true`), only `dj.Instance` works — global `dj.config/conn()` raise errors

## Key Quotes

> "dj.Instance is the right tool for any situation where global state is a problem: multi-tenant web apps, concurrent test suites, or worker processes that might interfere with each other."

## Connections

- [[configuration]] — thread-safe mode and Instance documentation
- [[whats-new-22]] — dj.Instance introduced in DataJoint 2.2
- [[deploy-production]] — production patterns using Instance

## Contradictions

None identified.
