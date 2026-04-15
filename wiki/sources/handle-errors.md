---
title: "Handle Errors"
type: source
tags: [how-to, computation, error-handling]
date: 2026-04-15
source_file: raw/handle-errors.md
---

## Summary

Guide for handling errors in DataJoint computations. `populate(suppress_errors=True)` continues processing after individual `make()` failures, storing full tracebacks in the jobs table. `jobs.errors` property retrieves failed jobs. `return_exception_objects=True` returns caught exceptions for programmatic handling. Individual keys can be retried or ignored.

## Key Claims

- `suppress_errors=True` catches exceptions from `make()` and stores traceback in `~~table_name.error_message`
- `table.jobs.errors` returns a query of failed jobs with their error messages
- Full Python traceback is stored in `error_stack` column of the jobs table
- `return_exception_objects=True` returns a list of (key, exception) tuples instead of raising
- To retry failed jobs: delete rows from the jobs error table; they will be reprocessed on next populate()
- To permanently ignore failures: insert a placeholder row in the completed jobs table

## Key Quotes

> "Use `suppress_errors=True` in production populate loops — you want processing to continue even if individual jobs fail. Review `table.jobs.errors` periodically."

## Connections

- [[ComputationModel]] — the make/populate execution model
- [[JobsSystem]] — where error state is stored
- [[distributed-computing]] — error handling in multi-worker scenarios
- [[errors]] — exception types that can occur

## Contradictions

None identified.
