---
name: api-design-async
description: API design for asynchronous agent systems. Covers WebSocket streaming, REST vs Event-Driven trade-offs, versioning, rate-limiting, reconnection, and resource cleanup.
license: MIT
allowed-tools: []
---

# API Design - Asynchronous & Event-Driven Systems (Layer 3)

## Overview

Autonomous agents require dual API patterns: REST for request/response (short-lived operations) and WebSocket/Server-Sent Events (SSE) for streaming (long-lived operations like agent execution, reasoning tracing, metrics). This skill captures patterns for hybrid async APIs.

## REST vs Event-Driven Trade-offs

### REST (Request/Response)
**Use for:** Short-lived operations, file uploads, discrete queries

```
Client                          Server
  │                              │
  ├─ POST /api/projects/analyze  │
  │                              ├─ Process (5s)
  │  200 OK + Results ←───────────┤
  │
```

**Pros:** Simple, stateless, cacheable, easy to test
**Cons:** Polling overhead, client waits for completion, no real-time updates

**Example:** Query project metrics
```python
@app.route("/api/projects/<id>/metrics", methods=["GET"])
def get_metrics(id):
    project = db.get(id)
    return {
        "lines_of_code": project.loc,
        "complexity": calculate_complexity(project),
        "dependencies": len(project.deps)
    }
```

### WebSocket (Full-Duplex Streaming)
**Use for:** Long-running operations, real-time updates, agent execution

```
Client                          Server
  │                              │
  ├─ WebSocket Connect ───────────→
  │  Connected ←─────────────────┤
  │                              │
  ├─ emit('execute_agent', {...})→
  │                              ├─ Agent Start
  │  stream('step_started', {...})←─┤
  │  stream('step_complete', {...})←─┤
  │  stream('metrics_update', {...})←─┤
  │  stream('execution_complete', {})←─┤
  │                              │
  ├─ Close ───────────────────→
  │
```

**Pros:** Real-time updates, low latency, bidirectional, single connection
**Cons:** Stateful, harder to scale (requires sticky sessions), reconnection logic

**Example:** Agent execution with streaming
```python
@socketio.on('execute_agent', namespace='/agent/execution')
def execute_agent_streaming(data):
    """Stream agent steps in real-time."""
    
    job_id = data['job_id']
    goal = data['goal']
    
    # Emit: execution started
    emit('step_started', {
        'phase': 'initialization',
        'timestamp': now()
    })
    
    # Agent analyzes
    analysis = agent.analyze(goal)
    emit('step_complete', {
        'phase': 'analysis',
        'duration_ms': analysis.duration
    })
    
    # Agent executes
    execution = agent.execute(goal, analysis)
    emit('step_complete', {
        'phase': 'execution',
        'duration_ms': execution.duration
    })
    
    # Emit: final result
    emit('execution_complete', {
        'result': execution.output,
        'success': True,
        'total_duration_ms': execution.total_duration
    })
```

## WebSocket Lifecycle & Patterns

### Complete Lifecycle

```
CONNECT
  ├─ Authenticate (optional token)
  ├─ Emit 'ready' event
  │
ACTIVE
  ├─ Client: emit('execute_agent', job_data)
  ├─ Server: Validate job_data
  │  ├─ If valid: begin execution
  │  ├─ If invalid: emit('error', error_details)
  │
STREAMING (Execution)
  ├─ Server: emit('step_started', {phase, timestamp})
  ├─ Server: emit('reasoning_trace', {decision, options, selected, confidence})
  ├─ Server: emit('llm_call', {model, tokens, cost})
  ├─ Server: emit('step_complete', {phase, duration_ms})
  ├─ Repeat until complete
  │
FINALIZE
  ├─ Server: emit('execution_complete', {result, success})
  │ OR
  ├─ Server: emit('execution_failed', {error, recovery_hint})
  │
DISCONNECT
  ├─ Cleanup: Cancel in-flight operations
  ├─ Cleanup: Release resources (queues, timers)
  ├─ Log: Final metrics to database
  │
```

### Implementation: Reconnection Logic

```python
class AgentExecutionNamespace(Namespace):
    def __init__(self):
        self.active_sessions: Dict[str, ExecutionSession] = {}
        self.job_queue: asyncio.Queue = asyncio.Queue()
    
    def on_connect(self, sid, environ):
        """Handle new WebSocket connection."""
        logger.info(f"Client connected: {sid}")
        emit('ready', {'session_id': sid})
        self.active_sessions[sid] = ExecutionSession(sid)
    
    def on_disconnect(self, sid):
        """Handle WebSocket disconnection."""
        logger.info(f"Client disconnecting: {sid}")
        
        session = self.active_sessions.pop(sid, None)
        if session and session.status == ExecutionStatus.EXECUTING:
            # Client disconnected mid-execution
            logger.warning(f"Interrupted execution: {session.job_id}")
            
            # Save partial state for reconnection
            self.save_checkpoint(session.job_id, session.state)
            
            # Cancel in-flight work
            session.cancel()
    
    def on_execute_agent(self, sid, data):
        """Begin agent execution with streaming."""
        try:
            job_id = data['job_id']
            goal = data['goal']
            
            session = self.active_sessions.get(sid)
            session.job_id = job_id
            
            # Check for resume (interrupted execution)
            checkpoint = self.load_checkpoint(job_id)
            if checkpoint:
                emit('resume_checkpoint', {
                    'checkpoint_id': checkpoint['id'],
                    'phase': checkpoint['phase'],
                    'progress': checkpoint['progress']
                })
                # User decides: resume or restart
                return
            
            # Stream execution
            self._stream_execution(sid, job_id, goal)
        
        except ValidationError as e:
            emit('error', {'type': 'validation', 'message': str(e)})
        except Exception as e:
            emit('error', {'type': 'internal', 'message': 'Execution failed'})
    
    def _stream_execution(self, sid, job_id, goal):
        """Execute agent and stream results."""
        session = self.active_sessions[sid]
        session.status = ExecutionStatus.EXECUTING
        
        try:
            emit('step_started', {
                'phase': 'analysis',
                'timestamp': now_iso()
            })
            
            analysis = self.agent.analyze(goal)
            emit('step_complete', {
                'phase': 'analysis',
                'duration_ms': analysis.duration
            })
            
            # More phases...
            
            emit('execution_complete', {
                'job_id': job_id,
                'success': True,
                'result': analysis.output
            })
            
            session.status = ExecutionStatus.COMPLETE
        
        except Exception as e:
            emit('execution_failed', {
                'job_id': job_id,
                'error': str(e),
                'recovery_hint': 'Reconnect to resume'
            })
            session.status = ExecutionStatus.FAILED
    
    def on_reconnect(self, sid, data):
        """Handle client reconnection."""
        job_id = data['job_id']
        checkpoint = self.load_checkpoint(job_id)
        
        if checkpoint:
            emit('checkpoint_loaded', {
                'job_id': job_id,
                'phase': checkpoint['phase'],
                'progress_pct': checkpoint['progress_pct']
            })
            # Resume execution from checkpoint
            self._resume_execution(sid, checkpoint)
        else:
            emit('error', {
                'message': f'No checkpoint found for job {job_id}'
            })
```

## Rate-Limiting & Throttling

### Per-Client Rate Limit
```python
class RateLimiter:
    def __init__(self, max_requests=10, window_seconds=60):
        self.max_requests = max_requests
        self.window_seconds = window_seconds
        self.requests: Dict[str, List[float]] = {}  # sid → [timestamp, ...]
    
    def check(self, sid):
        """Return True if client can make request, False if rate-limited."""
        now = time.time()
        
        if sid not in self.requests:
            self.requests[sid] = []
        
        # Remove old requests outside window
        self.requests[sid] = [
            ts for ts in self.requests[sid]
            if now - ts < self.window_seconds
        ]
        
        # Check limit
        if len(self.requests[sid]) >= self.max_requests:
            retry_after = (
                self.requests[sid][0] + self.window_seconds - now
            )
            return False, int(retry_after)
        
        self.requests[sid].append(now)
        return True, 0

# Usage
limiter = RateLimiter(max_requests=100, window_seconds=60)

@socketio.on('execute_agent')
def execute_agent(sid, data):
    allowed, retry_after = limiter.check(sid)
    
    if not allowed:
        emit('error', {
            'type': 'rate_limited',
            'retry_after_seconds': retry_after
        })
        return
    
    # Proceed with execution
```

## API Versioning

### URL-based (Recommended for WebSocket)
```python
# Version in namespace
@app.namespace('/agent/v1/execution', description='Agent Execution v1')
@app.namespace('/agent/v2/execution', description='Agent Execution v2')

# Clients choose version on connect
ws = new WebSocket('ws://server:8000/socket.io/?namespace=/agent/v2/execution')
```

### Header-based
```python
def on_connect(sid, environ):
    headers = environ.get('HTTP_HEADERS', {})
    api_version = headers.get('X-API-Version', 'v1')
    
    if api_version == 'v1':
        self.handler = ExecutionHandlerV1()
    elif api_version == 'v2':
        self.handler = ExecutionHandlerV2()
    else:
        emit('error', {'message': 'Unsupported API version'})
```

### Breaking Changes: Migration Path
```
Old (v1): emit('execution_complete', {result, success})
New (v2): emit('execution_complete', {result, success, metadata})

Server handles both:
  if client_version == 'v1':
      emit('execution_complete', {...})
  else:
      emit('execution_complete', {..., metadata={...}})
```

## Resource Cleanup Patterns

### Memory Management
```python
class ExecutionSession:
    def __init__(self, sid: str, max_lifetime_seconds=3600):
        self.sid = sid
        self.created_at = time.time()
        self.max_lifetime = max_lifetime_seconds
        self.buffers: List[bytes] = []  # Clean up
        self.timers: List[Timer] = []   # Cancel
    
    def is_expired(self) -> bool:
        return time.time() - self.created_at > self.max_lifetime
    
    def cleanup(self):
        """Release all resources."""
        for timer in self.timers:
            timer.cancel()
        
        self.buffers.clear()
        self.timers.clear()

# Periodic cleanup (every 5 minutes)
@app.before_request
def cleanup_expired_sessions():
    namespace = socketio.server.namespace_handlers['/agent/execution']
    
    expired_sids = [
        sid for sid, session in namespace.active_sessions.items()
        if session.is_expired()
    ]
    
    for sid in expired_sids:
        namespace.active_sessions[sid].cleanup()
        del namespace.active_sessions[sid]
        logger.info(f"Cleaned up expired session: {sid}")
```

## Implementation Checklist

```
REST Endpoints:
  [ ] GET /api/projects/<id>/metrics (quick queries)
  [ ] POST /api/projects/analyze (async job creation)
  [ ] GET /api/jobs/<job_id> (poll for completion)

WebSocket Setup:
  [ ] Define namespace (e.g., /agent/execution)
  [ ] on_connect: authenticate, emit 'ready'
  [ ] on_disconnect: cleanup, save state
  [ ] on_execute_agent: validate, stream execution
  [ ] on_reconnect: load checkpoint, resume

Streaming Events (per execution):
  [ ] 'step_started' {phase, timestamp}
  [ ] 'reasoning_trace' {decision, options, confidence}
  [ ] 'llm_call' {model, tokens, cost}
  [ ] 'step_complete' {phase, duration_ms}
  [ ] 'execution_complete' {result, success}
  [ ] 'execution_failed' {error, recovery_hint}
  [ ] 'error' {type, message}

Reconnection:
  [ ] Save execution checkpoint on disconnect
  [ ] Load checkpoint on reconnect
  [ ] Resume from phase (not restart)
  [ ] Emit 'checkpoint_loaded' event

Rate Limiting:
  [ ] Per-client request counter
  [ ] Window-based reset (e.g., 100 req/min)
  [ ] Reject excess with 'rate_limited' error
  [ ] Emit retry_after_seconds

Resource Cleanup:
  [ ] Cancel timers on disconnect
  [ ] Clear buffers on session end
  [ ] Periodic cleanup of expired sessions (>1hr)
  [ ] Log cleanup events for debugging

Versioning:
  [ ] Namespace versioning (/agent/v1, /agent/v2)
  [ ] Version detection on connect
  [ ] Graceful fallback for unsupported versions
  [ ] Migration path for breaking changes
```

## Full Details

See: `doc/backend/layer-3-autonomous-agent/WEBSOCKET_STREAMING_API.md`
