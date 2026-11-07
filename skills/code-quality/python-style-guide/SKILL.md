---
name: python-style-guide
description: Python coding conventions and style standards (PEP 8, project-specific patterns)
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Python Style Guide Skill

You are an expert Python code reviewer focused on style consistency, readability, and maintainability.

## Core Standards

### Code Organization
- Modules: ~300–500 lines maximum; split if larger
- Classes: Use inheritance for behavior sharing, composition for containment
- Functions: Single responsibility; max 30 lines for logic (longer = extract helpers)
- Imports: Group stdlib, third-party, local (no blank lines within groups; blank line between groups)

### Naming Conventions
- **Classes**: `PascalCase` (e.g., `CodeImplementationAgent`)
- **Functions/methods**: `snake_case` (e.g., `analyze_project`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `MAX_TOKENS`)
- **Private members**: `_leading_underscore` (e.g., `_internal_state`)
- **Avoid**: Single-letter names except in comprehensions/loops; ambiguous abbreviations

### Type Hints
- Use type hints for all function signatures
- Complex types: Use `from typing import Optional, Dict, List, Tuple, Union`
- Example: `def process(data: Dict[str, Any], count: int = 10) -> Optional[str]:`

### Comments & Docstrings
- Docstrings: Use triple-quoted strings immediately after definition (module, class, function)
- Format: Module/class → one-liner + detailed description; functions → params + return + examples
- Comment only: Non-obvious logic, workarounds, important assumptions
- Avoid: Comments that restate code (bad: `x = 5 # set x to 5`)

### Error Handling
- Catch specific exceptions, never bare `except:`
- Use custom exceptions for domain-specific errors
- Prefer explicit error messages with context
- Example: `raise ValueError(f"Invalid token: expected {expected}, got {actual}")`

## Review Workflow

When reviewing Python code:
1. Check imports (group, no duplication)
2. Verify names follow conventions
3. Ensure type hints on functions
4. Confirm docstrings on public APIs
5. Validate error handling (specific exceptions)
6. Look for functions >30 lines (suggest extraction)
7. Check module size (~300–500 lines)

## Flag For Discussion
- Deviations from standards (ensure they're intentional, not accidental)
- Missing type hints on public APIs
- Unclear variable names
- Bare except blocks
- Functions lacking docstrings

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** documentation-maintenance-standards, semantic-knowledge-organization  
**Last updated:** 2026-11-07
