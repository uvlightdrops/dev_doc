---
name: design-patterns-autonomy
description: Design patterns for autonomous agent orchestration. Covers orchestrator pattern, agent lifecycle state machines, self-correction loops, and event-driven coordination.
license: MIT
allowed-tools: []
---

# Design Patterns - Autonomous Agent Systems (Layer 3)

## Overview

Layer 3 autonomous agents require specific design patterns: orchestrators that delegate work, state machines for agent lifecycle, self-correction feedback loops, and event-driven communication. This skill captures proven patterns for multi-agent systems.

## Core Patterns

### 1. Orchestrator Pattern
Central coordinator manages multiple agents without direct coupling:

```python
class MetaCoordinator:
    """Orchestrates multiple specialized agents."""
    
    def __init__(self):
        self.agents: Dict[str, AgentBase] = {}
        self.workflows: Dict[str, WorkflowDefinition] = {}
    
    def register_agent(self, name: str, agent: AgentBase):
        """Register an agent (e.g., 'code-implementer', 'code-reviewer')."""
        self.agents[name] = agent
    
    def orchestrate(self, request: UserRequest) -> Result:
        """
        Route request through workflow of agents.
        
        Flow:
        1. Intent Detection: What does user want?
        2. Workflow Routing: Which workflow applies?
        3. Agent Selection: Which agent should run each step?
        4. Execution: Run agents sequentially/parallel
        5. Aggregation: Combine results
        6. Response Generation: Format for user
        """
        
        # Stage 1: Intent Detection
        intent = self.detect_intent(request)
        
        # Stage 2: Workflow Routing
        workflow = self.workflows.get(intent.workflow_name)
        if not workflow:
            raise ValueError(f"No workflow for intent: {intent}")
        
        # Stage 3-6: Execute workflow
        return self.execute_workflow(workflow, request, intent)
    
    def execute_workflow(self, workflow, request, intent):
        """Execute stages in sequence."""
        results = {}
        
        for stage in workflow.stages:
            # Select agent for this stage
            agent = self.select_agent(stage.agent_pool, request, results)
            
            # Execute stage
            stage_result = agent.execute(
                task=stage.task,
                input=request,
                context=results
            )
            
            results[stage.name] = stage_result
        
        return results
```

**Key Traits:**
- Agents don't know about each other
- Coordinator makes all decisions (routing, selection)
- Loose coupling enables easy agent addition/removal
- Transparent for testing (mock coordinator)

### 2. Agent Lifecycle State Machine
Formal state transitions prevent invalid operations:

```python
from enum import Enum

class AgentState(Enum):
    INITIALIZED = "initialized"
    ANALYZING = "analyzing"
    PLANNING = "planning"
    EXECUTING = "executing"
    FINALIZING = "finalizing"
    COMPLETE = "complete"
    FAILED = "failed"
    ERRORED = "errored"

class AgentStateMachine:
    """Enforce valid state transitions."""
    
    # Valid transitions: source → [valid_destinations]
    TRANSITIONS = {
        AgentState.INITIALIZED: [AgentState.ANALYZING],
        AgentState.ANALYZING: [AgentState.PLANNING, AgentState.ERRORED],
        AgentState.PLANNING: [AgentState.EXECUTING, AgentState.ERRORED],
        AgentState.EXECUTING: [AgentState.FINALIZING, AgentState.FAILED, AgentState.ERRORED],
        AgentState.FINALIZING: [AgentState.COMPLETE, AgentState.ERRORED],
        AgentState.COMPLETE: [],  # Terminal
        AgentState.FAILED: [],    # Terminal
        AgentState.ERRORED: [],   # Terminal
    }
    
    def __init__(self):
        self.state = AgentState.INITIALIZED
    
    def transition(self, target_state: AgentState):
        """Attempt state transition with validation."""
        if target_state not in self.TRANSITIONS[self.state]:
            raise IllegalStateTransition(
                f"Cannot go from {self.state} → {target_state}"
            )
        
        old_state = self.state
        self.state = target_state
        
        # Hooks for side effects
        self._on_state_enter(target_state)
        logger.info(f"Agent: {old_state} → {target_state}")
    
    def _on_state_enter(self, state: AgentState):
        """Perform actions when entering a state."""
        if state == AgentState.ANALYZING:
            self.metrics.start_phase("analysis")
            self.tracer.record_event(EventType.ANALYSIS_START)
        
        elif state == AgentState.EXECUTING:
            self.metrics.start_phase("execution")
            self.tracer.record_event(EventType.EXECUTION_START)
        
        elif state == AgentState.COMPLETE:
            self.metrics.end_phase()
            self.tracer.end_session(success=True)
```

**Benefits:**
- Prevents invalid transitions (can't execute before analyzing)
- Explicit state at any time (no guessing)
- Debugging: trace state history
- Testing: verify correct state progression

### 3. Self-Correction Feedback Loop
Agent improves own decisions through iteration:

```python
class SelfCorrectingAgent:
    """Execute, verify, correct until success or max attempts."""
    
    def execute_with_correction(self, goal: str, max_iterations=3) -> Result:
        """
        Loop: Execute → Verify → Feedback → Correct
        """
        
        current_result = None
        verification_score = 0
        
        for iteration in range(1, max_iterations + 1):
            self.tracer.record_event(
                EventType.ANALYSIS_START,
                metadata={"iteration": iteration}
            )
            
            # Stage 1: Generate implementation
            if iteration == 1:
                current_result = self.generate_initial_solution(goal)
            else:
                current_result = self.refine_solution(current_result, feedback)
            
            # Stage 2: Verify correctness
            feedback = self.verify_result(current_result)
            verification_score = feedback.confidence
            
            self.tracer.record_decision(
                decision="continue_iterating",
                options=["accept", "refine", "restart"],
                selected="refine" if verification_score < 0.8 else "accept",
                confidence=verification_score
            )
            
            # Check if good enough
            if verification_score >= 0.8:
                self.tracer.record_event(EventType.EXECUTION_COMPLETE)
                return Result(
                    value=current_result,
                    confidence=verification_score,
                    iterations=iteration
                )
            
            # Otherwise, get feedback for next iteration
            if iteration < max_iterations:
                feedback = self.request_correction_feedback(current_result)
            else:
                self.tracer.record_event(EventType.ERROR_OCCURRED)
                return Result(
                    value=current_result,
                    confidence=verification_score,
                    iterations=iteration,
                    status="partial"
                )
        
        return current_result
    
    def verify_result(self, result) -> Feedback:
        """Check correctness (syntax, logic, tests)."""
        checks = {
            "syntax": self.check_syntax(result),
            "logic": self.check_logic(result),
            "tests": self.check_tests(result),
        }
        
        confidence = sum(checks.values()) / len(checks)
        issues = [k for k, v in checks.items() if v < 0.8]
        
        return Feedback(confidence=confidence, issues=issues)
```

**Loop Flow:**
```
Generate → Verify → Feedback (low confidence?)
                   ↓ Yes
              Correct/Refine
                   ↓
                Generate → Verify → Feedback (high confidence?)
                                   ↓ Yes
                              Accept Result
```

### 4. Observer Pattern for Event Streaming
Agents emit events; observers (WebSocket, logging) receive them:

```python
class EventEmitter:
    """Publish events for real-time subscribers."""
    
    def __init__(self):
        self.listeners: Dict[str, List[Callable]] = {}
    
    def on(self, event_type: str, callback: Callable):
        """Subscribe to event type."""
        if event_type not in self.listeners:
            self.listeners[event_type] = []
        self.listeners[event_type].append(callback)
    
    def emit(self, event_type: str, data: dict):
        """Publish event to all listeners."""
        if event_type in self.listeners:
            for callback in self.listeners[event_type]:
                try:
                    callback(data)
                except Exception as e:
                    logger.error(f"Event callback failed: {e}")

class AgentWithStreaming(AgentBase):
    def __init__(self):
        self.emitter = EventEmitter()
    
    def execute(self, task):
        # Subscribe frontend to real-time events
        self.emitter.on("execution_step", lambda e: websocket.emit("step", e))
        self.emitter.on("llm_call", lambda e: websocket.emit("cost_update", e))
        self.emitter.on("error", lambda e: websocket.emit("error", e))
        
        # Execute, emitting events
        for step in self.steps:
            self.emitter.emit("execution_step", {
                "step_name": step.name,
                "phase": "in_progress"
            })
            
            result = step.execute()
            
            self.emitter.emit("execution_step", {
                "step_name": step.name,
                "phase": "complete",
                "result": result
            })
```

## Implementation Checklist

```
Orchestrator Pattern:
  [ ] Create MetaCoordinator class
  [ ] Agent registration system
  [ ] Workflow definition structure
  [ ] Stage-by-stage execution
  [ ] Loose coupling (agents don't reference each other)
  [ ] Context passing between agents

State Machine:
  [ ] Define AgentState enum (INIT, ANALYZING, EXECUTING, COMPLETE, etc.)
  [ ] TRANSITIONS mapping (valid_next_states per state)
  [ ] transition() method with validation
  [ ] _on_state_enter() hooks for side effects
  [ ] Illegal state detection + exception
  [ ] Log state transitions for debugging

Self-Correction Loop:
  [ ] generate_initial_solution(goal)
  [ ] verify_result(result) → confidence score
  [ ] request_correction_feedback(result) → issues
  [ ] refine_solution(result, feedback)
  [ ] Iteration counter with max_iterations limit
  [ ] Return partial result if max iterations exceeded

Event Streaming (Observer):
  [ ] EventEmitter with on(event_type, callback)
  [ ] emit(event_type, data) publishes to listeners
  [ ] Error handling for failed callbacks
  [ ] WebSocket integration for real-time push
  [ ] Trace logging for event history
```

## State Transitions Example

```
User Request
     ↓
[INITIALIZED]
     ↓
[ANALYZING] ← tracer: record analysis events
     ↓
[PLANNING]  ← tracer: record decisions
     ↓
[EXECUTING] ← tracer: record execution steps
             ← llm_tracker: record API calls
             ← metrics: time each phase
             ← WebSocket: emit real-time events
     ↓
[FINALIZING] ← Save all traces/metrics
     ↓
[COMPLETE] or [FAILED]
     ↓
Return Result to User
```

## Full Details

See: `doc/backend/layer-3-autonomous-agent/AGENT_LIFECYCLE_STATE_MACHINE.md`
