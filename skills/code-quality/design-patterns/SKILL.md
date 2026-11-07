---
name: design-patterns
description: Common design patterns used across projects (Factory, Strategy, Observer, Builder)
license: CC-BY-4.0
allowed-tools:
  - grep
  - bash
---

# Design Patterns Skill

You are an expert in recognizing and applying design patterns to improve code structure and reusability.

## Patterns Used in Projects

### 1. Factory Pattern
- **Use when:** Creating objects with complex initialization or multiple variants
- **Example:** `AgentFactory` in kicli-code-assist returns different agent types
- **Structure:** Factory method returns interface; caller doesn't know concrete class

### 2. Strategy Pattern
- **Use when:** Algorithm needs to vary at runtime
- **Example:** `ConfigResolver` with merge strategies (smart merge, override, append)
- **Structure:** Strategy interface + concrete implementations; context holds strategy

### 3. Observer Pattern
- **Use when:** Loose coupling between event producers and consumers
- **Example:** WebSocket listeners on agent execution events
- **Structure:** Observable broadcasts events; observers register callbacks

### 4. Builder Pattern
- **Use when:** Object construction is complex with many optional parameters
- **Example:** Query builders for database operations or config construction
- **Structure:** Builder accumulates configuration, then `build()` returns final object

### 5. Adapter Pattern
- **Use when:** Integrating external libraries with different interfaces
- **Example:** `AsyncOpenAIClient` adapts OpenAI SDK for async operations
- **Structure:** Adapter wraps external interface, exposes internal interface

## Recognition Checklist

When reviewing code, identify these patterns:
- [ ] Multiple related classes that could use inheritance or composition?
- [ ] Complex object creation logic that should use Factory?
- [ ] Switching based on object type → could use Strategy?
- [ ] Listeners/callbacks → Observer pattern?
- [ ] Many optional parameters → consider Builder?
- [ ] External library integration → Adapter pattern?

## Anti-Patterns to Avoid

- God Objects: Classes doing too much (>500 lines, >10 public methods)
- Tight Coupling: Direct dependencies on concrete classes instead of interfaces
- Callback Hell: Nested callbacks → use async/await or event-driven architecture
- DRY Violations: Repeated code blocks → extract to function/class

## Documentation

When suggesting a pattern:
1. Name the pattern clearly
2. Show before/after code structure
3. Explain benefits (maintainability, reusability, testability)
4. Link to reference documentation

---

**Used in projects:** kicli-code-assist, ki-knowledge, ki-core  
**Related skills:** python-style-guide, semantic-knowledge-organization  
**Last updated:** 2026-11-07
