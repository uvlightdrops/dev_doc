---
name: test-coverage
description: Coverage reporting, coverage goals, and meaningful coverage metrics
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Test Coverage Skill

You are an expert in measuring test coverage meaningfully and setting realistic coverage targets.

## Core Principles

### 1. Coverage Metrics
- **Line coverage:** How many lines executed by tests
- **Branch coverage:** How many conditional branches taken
- **Function coverage:** How many functions called
- Reality: 100% line coverage ≠ well-tested code (unreachable branches, missed edge cases)

### 2. Coverage Goals
- **Production code:** Aim for 70–80% line coverage
  - Critical paths (auth, data processing, error handling): 90%+
  - Utility functions, constants, simple getters: 60–70% acceptable
- **Test utilities:** Lower expectations (90%+ achievable, less critical)
- **Config files, migrations:** Usually exempt from coverage

### 3. Meaningful Coverage
Focus on:
- All error paths (exceptions, error codes)
- Boundary conditions (zero, max, off-by-one)
- Different input types (empty, null, valid, invalid)
- State transitions (object lifecycle)

Ignore:
- Dead code (refactor instead of testing it)
- Platform-specific branches (hard to test in CI)
- Third-party library code (test integration, not library)

## Using pytest-cov

```bash
# Run tests with coverage report
pytest --cov=src --cov-report=html --cov-report=term

# Show missing lines
pytest --cov=src --cov-report=term-missing

# Coverage for specific module
pytest --cov=src/config --cov-report=html

# Fail if coverage below threshold
pytest --cov=src --cov-fail-under=70
```

## Coverage Configuration (pyproject.toml)

```toml
[tool.coverage.run]
branch = true
omit = [
    "*/tests/*",
    "*/__pycache__/*",
    "*/venv/*",
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
]
fail_under = 70
```

## Review Checklist

- [ ] Coverage goal defined (70–80% typically)?
- [ ] Critical paths have 90%+ coverage?
- [ ] Error paths tested?
- [ ] Boundary conditions covered?
- [ ] Coverage report accessible (CI/CD or README)?
- [ ] Exclusions documented?
- [ ] No dead code (coverage gap → refactor)?

## Anti-Patterns

- Striving for 100% coverage (unrealistic, low ROI)
- Ignoring coverage of error paths (most bugs hide there)
- Excluding code from coverage without justification
- Measuring coverage at test-file level (measure production code)
- Treating coverage as quality metric (high coverage ≠ good tests)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** unit-testing, integration-testing  
**Last updated:** 2026-11-07
