---
title: "How to write integration tests for DataJoint tables"
type: synthesis
tags: [testing, integration-test, fixtures, pytest, dj.Instance]
sources: [testing, use-instances, configure-database, manage-pipeline-project]
last_updated: 2026-04-17
---

## How to write integration tests for DataJoint tables

### The core rule: never mock the database

[[testing]] is explicit:

> "Don't mock the database. DataJoint's correctness depends on real SQL semantics — foreign key constraints, type coercions, and query algebra all need a real database to test correctly."

Mocking `dj.conn()` or patching table methods masks the bugs that matter most: FK cascade behavior, type coercions on insert, populate() key-source logic, and query operator semantics. Integration tests must hit a real MySQL or PostgreSQL instance.

---

### Spin up a real DB in CI with `testcontainers`

```bash
pip install testcontainers[mysql]
```

```python
# conftest.py
import pytest, datajoint as dj
from testcontainers.mysql import MySqlContainer

@pytest.fixture(scope="session")
def mysql_container():
    with MySqlContainer("mysql:8.0") as container:
        yield container

@pytest.fixture(scope="session")
def dj_connection(mysql_container):
    return dj.Instance(
        host=mysql_container.get_container_host_ip(),
        port=int(mysql_container.get_exposed_port(3306)),
        user="test",
        password="test",
    )
```

Session-scoped so the container starts once per test run. Per [[use-instances]], `dj.Instance` creates an isolated Config+Connection pair independent of global `dj.config` — parallel test workers don't interfere.

---

### Testing pre-defined tables (without rewriting definitions)

The table `definition` string must live in exactly one place. There are three ways to reuse existing table definitions in tests.

#### Approach 1 — Factory function (best; requires one refactor)

Structure each pipeline module to expose a `register(schema)` factory instead of decorating at module level:

```python
# my_pipeline/mouse.py
import datajoint as dj

def register(schema):
    @schema
    class Mouse(dj.Manual):
        definition = """
        mouse_id : int
        ---
        birth_date : date
        """
    @schema
    class Session(dj.Manual):
        definition = """
        -> Mouse
        session_idx : int
        ---
        session_date : date
        """
    return Mouse, Session
```

Production startup:
```python
raw_inst = dj.Instance(host=..., user=..., password=...)
Mouse, Session = register(raw_inst.Schema('lab_raw'))
```

Test fixture — **no definitions repeated**:
```python
import uuid
from my_pipeline.mouse import register

@pytest.fixture
def tables(dj_connection):
    schema = dj_connection.Schema(f"test_{uuid.uuid4().hex[:8]}")
    Mouse, Session = register(schema)
    yield Mouse, Session
    schema.drop(force=True)
```

#### Approach 2 — Re-decorate with `test_schema(ExistingClass)`

If production modules already use module-level `@schema`, re-register the existing class under a test schema. `@schema` is syntactic sugar for `Mouse = schema(Mouse)` — calling it again with a different schema object creates a new binding without touching the original:

```python
from my_pipeline.mouse import Mouse, Session   # bound to prod schema

@pytest.fixture
def test_tables(dj_connection):
    test_schema = dj_connection.Schema(f"test_{uuid.uuid4().hex[:8]}")
    TestMouse   = test_schema(Mouse)     # inherits definition; creates table in test schema
    TestSession = test_schema(Session)
    yield TestMouse, TestSession
    test_schema.drop(force=True)
```

`TestMouse` and `TestSession` are new schema-bound objects in the test DB. `Mouse` and `Session` production bindings are unaffected. No `definition` strings are repeated.

#### Approach 3 — `inst.FreeTable` for assertion-only access

Per [[use-instances]], `inst.FreeTable("schema.table")` accesses an existing table by name with no class definition required. Has no `make()` or `populate()` — plain query/insert only. Useful for asserting on side-effects.

```python
def test_mouse_exists(dj_connection):
    mouse = dj_connection.FreeTable("test_abc.mouse")
    assert len(mouse & {'mouse_id': 99}) == 1
```

---

### Approach comparison

| Approach | Requires code change | Handles FK chains | Handles `populate()` | Best for |
|---|---|---|---|---|
| Factory function | Yes — one refactor | Yes | Yes | New or refactorable pipelines |
| Re-decorate `test_schema(Cls)` | No | Yes | Yes | Existing module-level `@schema` code |
| `inst.FreeTable` | No | Manual | No | Spot-checks and assertion helpers |

---

### Testing `populate()` and `make()`

```python
def test_analysis_populate(test_tables):
    Mouse, Session = test_tables
    # also need Analysis bound to same test schema — use same factory or re-decorate
    Session.insert([{'mouse_id': 1, 'session_idx': i, 'session_date': '2024-01-01'} for i in range(3)])
    Analysis.populate()
    assert len(Analysis) == 3
```

To assert on error handling:
```python
Analysis.populate(reserve_jobs=True, suppress_errors=True)
errors = Analysis.jobs.errors.fetch(as_dict=True)
assert len(errors) == 1
assert 'expected error text' in errors[0]['error_message']
```

---

### Testing external object storage

Configure a temp-directory file store on the test instance — no cloud credentials needed:

```python
@pytest.fixture(scope="session")
def dj_connection(mysql_container, tmp_path_factory):
    store_dir = tmp_path_factory.mktemp("dj_store")
    return dj.Instance(
        host=mysql_container.get_container_host_ip(),
        port=int(mysql_container.get_exposed_port(3306)),
        user="test", password="test",
        stores={"analysis_store": {"protocol": "file", "location": str(store_dir)}}
    )
```

Tables using `<npy@analysis_store>` write to `store_dir` during tests.

---

### GitHub Actions CI

```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.11" }
      - run: pip install -e ".[test]"
      - run: pytest tests/ -v
        env:
          TESTCONTAINERS_RYUK_DISABLED: "true"
```

Docker is available on `ubuntu-latest` runners — `testcontainers` uses it automatically.

---

## Sources

- [[testing]] — testcontainers pattern, fixture structure, CI config, "don't mock the DB"
- [[use-instances]] — `dj.Instance`, `inst.Schema()`, `inst.FreeTable()`
- [[Schema]] — `@schema` as a callable; `create_tables` behavior
- [[manage-pipeline-project]] — one-module-per-schema project structure
