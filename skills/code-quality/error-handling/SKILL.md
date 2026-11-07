---
name: error-handling
description: Structured error handling with recovery hints, context preservation, and logging
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Error Handling Skill

You are an expert in designing robust error handling that aids debugging and enables recovery.

## Core Principles

### 1. Specific Exception Types
- Create custom exceptions for domain errors (not generic Exception/RuntimeError)
- Example: `ConfigurationError`, `ValidationError`, `LLMError`
- Include context in exception messages: what failed, why, what was expected

### 2. Recovery Hints
- When catching errors, provide actionable hints for recovery
- Example: `"Config key 'model' missing. Check ~/.kicli/config.yaml or set MODEL env var"`
- Include: problem + context + solution

### 3. Error Context
- Preserve stack traces (don't swallow with bare `except:`)
- Add contextual data: what operation was attempted, with what inputs (sanitized)
- Example: `raise ProcessingError(f"Failed to parse {file_path}") from e`

### 4. Logging
- Log errors at appropriate level: DEBUG (recoverable), WARNING (degraded), ERROR (failure)
- Include: timestamp, error type, message, stack trace (debug level), context
- Never log secrets (passwords, API keys, tokens)

## Error Handling Patterns

### Try-Except-Else
```python
try:
    result = risky_operation()
except SpecificError as e:
    # Recovery hint + logging
    logger.error(f"Operation failed: {e}. Try: restart the service")
    return None
else:
    # Success path
    return result
```

### Context Managers
```python
with error_context(operation="data_sync", entity_id=user_id):
    perform_sync()  # Errors automatically tagged with context
```

### Validation Before Processing
```python
try:
    validator.validate(input_data)
except ValidationError as e:
    raise ValueError(f"Invalid input: {e.message}. Expected format: {e.expected}")
```

## Review Checklist

When reviewing error handling:
- [ ] Custom exceptions used (not generic Exception)?
- [ ] Error messages include context + recovery hint?
- [ ] Stack traces preserved (no bare `except:` or `except Exception:`)?
- [ ] Sensitive data sanitized before logging?
- [ ] Recovery attempts are reasonable?
- [ ] Async errors propagated correctly?

## Anti-Patterns

- Swallowing errors silently (except with explicit justification)
- Generic error messages ("An error occurred")
- Logging entire stack traces at INFO level (use DEBUG)
- Including secrets in error messages/logs
- Re-raising without context (add `from e`)

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** python-style-guide, documentation-maintenance-standards  
**Last updated:** 2026-11-07
