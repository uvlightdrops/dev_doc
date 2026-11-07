---
name: integration-testing
description: Integration test design for multi-component workflows and external system interactions
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Integration Testing Skill

You are an expert in designing integration tests that verify components work together correctly.

## Core Principles

### 1. Scope
- Integration tests: 2+ components interacting (e.g., API + database, service + message queue)
- NOT: Mocking everything (that's a unit test); NOT: Testing third-party libraries
- Focus: Data flow, state transitions, error propagation across boundaries

### 2. Test Environment
- Use test databases (real DB, separate schema or in-memory SQLite)
- Use Docker containers for external services (Postgres, Redis, Kafka)
- Isolate test runs: each test gets clean state or rolls back changes
- No shared state between tests

### 3. Test Data Management
- Fixtures: Reusable test data setup (users, projects, configurations)
- Factories: Generate realistic test data (address, user profiles)
- Cleanup: Delete test data after test or use transactions + rollback

### 4. Async Testing
- Use `pytest-asyncio` for async test functions
- `async def test_...()` with `await` calls
- Mock async operations carefully (return awaitables)

### 5. HTTP/API Testing
- Use `requests` or `httpx` for real HTTP calls
- Or: Mock HTTP server with `responses` library
- Verify: Status codes, response bodies, headers

## Test Structure

```python
import pytest
import asyncio
from database import get_session, User

@pytest.fixture
def test_db():
    """Fixture: in-memory test database."""
    db = Database(':memory:')
    yield db
    db.close()

@pytest.fixture
def user_factory():
    """Factory: create test users."""
    def _create(name="Alice", email="alice@example.com"):
        return User(name=name, email=email)
    return _create

class TestUserAPI:
    """Integration tests for User API + Database."""
    
    @pytest.mark.asyncio
    async def test_create_user_api_persists_to_db(self, test_db, user_factory):
        """Verify API write reaches database correctly."""
        async with httpx.AsyncClient(app=app) as client:
            response = await client.post(
                "/users",
                json={"name": "Alice", "email": "alice@example.com"}
            )
            assert response.status_code == 201
            
            # Verify database state
            user = test_db.get_user_by_email("alice@example.com")
            assert user.name == "Alice"
```

## Review Checklist

- [ ] Test describes multi-component flow (not single unit)?
- [ ] External services/DBs set up and torn down cleanly?
- [ ] No hard-coded delays (use proper async/await)?
- [ ] Test data isolated (doesn't affect other tests)?
- [ ] Assertions verify state across boundaries?
- [ ] Error scenarios tested (API errors, DB failures)?
- [ ] Performance acceptable (not testing slowness, but not O(n²))?

## Anti-Patterns

- Mocking entire dependency (defeats purpose of integration test)
- Relying on test execution order
- Real production databases in test suite
- Slow tests blocking CI/CD (>5 sec per test)
- Over-detailed assertions (test behavior, not implementation)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** unit-testing, error-handling  
**Last updated:** 2026-11-07
