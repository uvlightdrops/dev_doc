---
name: unit-testing
description: Unit test design, isolation, and fast feedback patterns
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Unit Testing Skill

You are an expert in designing fast, focused unit tests that verify behavior in isolation.

## Core Principles

### 1. Test Isolation
- Mock external dependencies (database, API, file system)
- Each test verifies one behavior
- No shared state between tests
- Setup/teardown clear resources

### 2. Naming
- Format: `test_<subject>_<scenario>_<expected_result>`
- Example: `test_config_parser_with_missing_key_raises_error`
- Avoid: `test_1`, `test_parser`, generic names

### 3. AAA Pattern (Arrange-Act-Assert)
```python
def test_user_age_calculation():
    # Arrange: set up data
    user = User(birth_year=2000)
    
    # Act: perform operation
    age = user.get_age()
    
    # Assert: verify result
    assert age == 24
```

### 4. Mocking Strategy
- Mock: External services, I/O, time-based behavior
- Spy: Verify interactions without preventing execution
- Stub: Return fixed data
- Use `unittest.mock.patch()` for monkey-patching

### 5. Assertions
- Be specific: `assert result == 5` not `assert result`
- Use helper assertions: `assertIn`, `assertRaises`, `assertAlmostEqual`
- Clear failure messages: `assert value > 0, f"Expected positive, got {value}"`

## Test Structure

```python
import pytest
from unittest.mock import Mock, patch

class TestUserService:
    """Tests for UserService class."""
    
    def setup_method(self):
        """Setup before each test."""
        self.service = UserService()
    
    def test_create_user_stores_correctly(self):
        """Verify user creation persists data."""
        user = self.service.create_user("alice", "alice@example.com")
        assert user.name == "alice"
        assert user.email == "alice@example.com"
    
    @patch('user_service.EmailClient')
    def test_create_user_sends_welcome_email(self, mock_email):
        """Verify welcome email sent after user creation."""
        self.service.create_user("bob", "bob@example.com")
        mock_email.send.assert_called_once()
```

## Review Checklist

- [ ] Test name clearly describes behavior?
- [ ] One assertion or related assertions testing one behavior?
- [ ] External dependencies mocked?
- [ ] Test data realistic but minimal?
- [ ] No flaky assertions (time-dependent, random)?
- [ ] Setup/teardown clean resources?
- [ ] Shared fixtures or conftest.py used?

## Anti-Patterns

- Testing multiple behaviors in one test
- Incomplete mocking (leaks to real I/O)
- Brittle assertions (exact string matches, fragile ordering)
- Skipped tests without justification
- Tests that depend on execution order
- Over-testing (testing framework, not user code)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** error-handling, design-patterns  
**Last updated:** 2026-11-07
