---
name: error-handling-layer3
description: Multi-agent error handling with resilience patterns. Covers agent failure modes, circuit breaker, recovery strategies, and logging for autonomous systems.
license: MIT
allowed-tools: []
---

# Error Handling - Layer 3 Autonomous Agents

## Overview

Layer 3 (autonomous agents) introduces distributed failure modes: agent crashes, LLM timeouts, WebSocket disconnects, queue overflows. This skill defines resilience patterns for self-healing autonomous systems.

## Core Principles

### 1. Error Hierarchy for Agents
Define custom exception classes, not generic `Exception`:

```python
# Base hierarchy
class AgentError(Exception):
    """Base for all agent-related errors."""
    def __init__(self, message: str, recovery_hint: str = None, context: dict = None):
        self.message = message
        self.recovery_hint = recovery_hint  # What to try next
        self.context = context or {}  # Debug context

class AgentExecutionError(AgentError):
    """Agent failed during execution (code error, timeout, crash)."""
    pass

class TransparencyModuleError(AgentError):
    """Tracing/metrics collection failed."""
    pass

class WebSocketStreamingError(AgentError):
    """Real-time event streaming failed."""
    pass

class LLMCallError(AgentError):
    """LLM API call failed."""
    pass

class CircuitBreakerOpen(AgentError):
    """Too many failures; circuit breaker activated."""
    pass
```

### 2. Circuit Breaker Pattern
Stop retrying after repeated failures to prevent cascade:

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=3, recovery_timeout_sec=60):
        self.failure_count = 0
        self.failure_threshold = failure_threshold
        self.state = "closed"  # closed → open → half-open → closed
        self.last_failure_time = None
        self.recovery_timeout_sec = recovery_timeout_sec

    def record_success(self):
        self.failure_count = 0
        self.state = "closed"

    def record_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        if self.failure_count >= self.failure_threshold:
            self.state = "open"
            raise CircuitBreakerOpen(
                f"Circuit breaker open after {self.failure_count} failures",
                recovery_hint="Wait 60s for recovery, then retry"
            )

    def allow_request(self) -> bool:
        """Check if request should proceed."""
        if self.state == "closed":
            return True

        if self.state == "open":
            # Check if recovery timeout elapsed
            if time.time() - self.last_failure_time > self.recovery_timeout_sec:
                self.state = "half-open"
                return True  # Try one request
            return False

        return True  # half-open: allow request
```

### 3. Recovery Strategies

**Exponential Backoff (with jitter):**
```python
def exponential_backoff(attempt: int, max_wait_sec: int = 300):
    """Wait between retries, growing exponentially."""
    wait_sec = min(2 ** attempt, max_wait_sec)  # 1s, 2s, 4s, ..., 300s max
    jitter = random.uniform(0, wait_sec * 0.1)  # Add ±10% jitter
    return wait_sec + jitter
```

**Retry with Context:**
```python
def retry_with_backoff(func, args, max_attempts=3):
    """Retry function with exponential backoff."""
    for attempt in range(max_attempts):
        try:
            return func(*args)
        except AgentExecutionError as e:
            if attempt == max_attempts - 1:
                raise

            wait = exponential_backoff(attempt)
            logger.warning(
                f"Attempt {attempt+1}/{max_attempts} failed. "
                f"Retrying in {wait:.1f}s. Recovery hint: {e.recovery_hint}",
                extra={"error_context": e.context}
            )
            time.sleep(wait)
```

**Fallback Strategies:**
```python
# Strategy 1: Degraded Mode
if llm_call_fails:
    use_cached_response()  # Serve cached result instead
    log_warning("LLM unavailable, using cached result")

# Strategy 2: Human-in-Loop
if agent_confidence < 0.5:
    request_user_confirmation()  # Ask human to approve

# Strategy 3: Rollback to Previous State
if execution_fails_after_changes:
    rollback_changes()
    restore_previous_state()
    return error_with_partial_result
```

### 4. Logging & Tracing for Debug

Every error must include context for troubleshooting:

```python
class ErrorContext:
    def __init__(self):
        self.data = {
            "timestamp": datetime.utcnow().isoformat(),
            "execution_id": get_current_execution_id(),
            "agent_name": get_current_agent_name(),
            "phase": get_current_phase(),
            "retry_count": 0,
            "error_chain": []  # Stack of errors
        }

    def add_error(self, error: AgentError):
        self.data["error_chain"].append({
            "type": error.__class__.__name__,
            "message": error.message,
            "recovery_hint": error.recovery_hint,
            "context": error.context
        })

    def to_log_entry(self) -> dict:
        return self.data

# Usage
ctx = ErrorContext()
try:
    agent.execute()
except AgentExecutionError as e:
    ctx.add_error(e)
    logger.error(f"Agent execution failed: {e.message}", extra=ctx.to_log_entry())
```

### 5. Multi-Agent Failure Modes

Common scenarios in Layer 3:

| Failure Mode | Cause | Detection | Recovery |
|---|---|---|---|
| **Agent Crash** | Process dies, uncaught exception | Process monitor, heartbeat timeout | Restart agent, replay queue |
| **LLM Timeout** | API unresponsive (>30s) | Timer + exception | Circuit breaker, use cache |
| **WebSocket Disconnect** | Network failure | Socket exception | Reconnect with backoff |
| **Queue Overflow** | Events faster than processing | Queue size > limit | Drop oldest events (with warning) |
| **Trace Save Failure** | Disk full, permission denied | File I/O exception | Log error, continue execution |
| **Dependency Unavailable** | Database down, service unreachable | Connection exception | Fallback to local cache |

## Implementation Checklist

```
Error Hierarchy:
  [ ] Create custom exception classes (Agent, Execution, Transparency, WebSocket)
  [ ] All exceptions inherit from AgentError
  [ ] All exceptions include recovery_hint + context dict
  [ ] Document exception hierarchy in architecture doc

Circuit Breaker:
  [ ] Implement CircuitBreaker class (closed → open → half-open → closed)
  [ ] Failure threshold configurable (default 3)
  [ ] Recovery timeout configurable (default 60s)
  [ ] Track failure_count and last_failure_time
  [ ] Raise CircuitBreakerOpen when threshold exceeded

Recovery Strategies:
  [ ] Implement exponential_backoff(attempt, max_wait) with jitter
  [ ] Implement retry_with_backoff(func, max_attempts=3)
  [ ] Document fallback options (cache, human-in-loop, rollback)
  [ ] Add strategy selection based on error type

Logging & Context:
  [ ] ErrorContext class captures execution_id, phase, agent_name
  [ ] All errors logged with full context (not just message)
  [ ] Error chain preserved (trace of cascading failures)
  [ ] Structured logging for log aggregation tools

Multi-Agent Testing:
  [ ] Test agent crash + restart scenario
  [ ] Test LLM timeout with circuit breaker
  [ ] Test WebSocket disconnect + reconnect
  [ ] Test queue overflow handling
  [ ] Test partial failure recovery
```

## Example: Protected LLM Call

```python
class ResilientLLMClient:
    def __init__(self, model: str):
        self.model = model
        self.circuit_breaker = CircuitBreaker(failure_threshold=3)
        self.cache = {}

    def call(self, prompt: str, max_retries=3) -> str:
        cache_key = hash(prompt)

        try:
            # Check circuit breaker
            if not self.circuit_breaker.allow_request():
                raise CircuitBreakerOpen(
                    "LLM circuit breaker is open",
                    recovery_hint="Wait for recovery timeout, then retry"
                )

            # Try LLM call with timeout
            response = self._call_with_timeout(prompt, timeout_sec=30)
            self.circuit_breaker.record_success()
            self.cache[cache_key] = response
            return response

        except LLMCallError as e:
            self.circuit_breaker.record_failure()

            # Fallback to cache
            if cache_key in self.cache:
                logger.warning(
                    f"LLM call failed, using cached response. Error: {e.message}"
                )
                return self.cache[cache_key]

            raise AgentExecutionError(
                f"LLM call failed and no cache available",
                recovery_hint="Check LLM service status and retry",
                context={"error": str(e), "model": self.model}
            )
```

## Full Details

See: `doc/backend/layer-3-autonomous-agent/ERROR_HANDLING_RESILIENCE.md`
