# Shell Execution Patterns & Best Practices

**Status:** Reference guide for shell execution in Python applications  
**Last Updated:** 2026-10-20  
**Scope:** Cross-project - adapted for each application

## Overview

Executing shell commands from Python applications requires careful handling of:
- Process lifecycle (start, stop, signal handling)
- Input/output streaming
- Error handling and exit codes
- Async vs synchronous execution
- Security (command injection prevention)
- Resource cleanup

This guide documents patterns for robust shell execution.

---

## 1. Basic Synchronous Execution

### Simple Command Execution

```python
import subprocess

# Simplest: capture output
result = subprocess.run(
    ["ls", "-la", "/tmp"],
    capture_output=True,
    text=True,
    timeout=10,
)

if result.returncode == 0:
    print(result.stdout)
else:
    print(f"Error: {result.stderr}")
```

**Key Points:**
- `capture_output=True` captures stdout and stderr
- `text=True` returns strings (not bytes)
- `timeout` prevents hanging
- Check `returncode` for success/failure

### Handling Errors

```python
import subprocess

try:
    result = subprocess.run(
        ["docker", "ps"],
        capture_output=True,
        text=True,
        timeout=5,
        check=True,  # Raises CalledProcessError if returncode != 0
    )
except subprocess.CalledProcessError as e:
    print(f"Command failed: {e.stderr}")
except subprocess.TimeoutExpired:
    print("Command timed out")
```

---

## 2. Asynchronous Execution

### Using asyncio.create_subprocess_exec

```python
import asyncio

async def run_command_async(cmd: list[str]) -> tuple[int, str, str]:
    """Execute command asynchronously, return (returncode, stdout, stderr)"""
    try:
        proc = await asyncio.create_subprocess_exec(
            *cmd,
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE,
        )
        
        stdout, stderr = await asyncio.wait_for(
            proc.communicate(),
            timeout=10,
        )
        
        return proc.returncode, stdout.decode(), stderr.decode()
    
    except asyncio.TimeoutError:
        proc.kill()
        raise
    except Exception as e:
        raise

# Usage
async def main():
    code, out, err = await run_command_async(["python", "--version"])
    print(out)

asyncio.run(main())
```

**Advantages:**
- Doesn't block event loop
- Can run multiple commands concurrently
- Better for I/O-heavy workloads

---

## 3. Streaming Output (Real-time Feedback)

### Stream Output Line-by-Line

```python
import subprocess

def stream_command(cmd: list[str], callback=None):
    """Execute command and stream output line-by-line"""
    process = subprocess.Popen(
        cmd,
        stdout=subprocess.PIPE,
        stderr=subprocess.STDOUT,
        text=True,
        bufsize=1,  # Line-buffered
    )
    
    try:
        for line in process.stdout:
            line = line.rstrip('\n')
            print(f"OUTPUT: {line}")
            if callback:
                callback(line)
    
    finally:
        process.wait()
        return process.returncode

# Usage
stream_command(["npm", "run", "build"], callback=lambda line: print(f"[BUILD] {line}"))
```

**Use Cases:**
- Long-running commands (build, deployment)
- Real-time feedback to user
- Log aggregation

### Async Streaming (Better for TUI/Web)

```python
import asyncio

async def stream_command_async(cmd: list[str]):
    """Stream output asynchronously"""
    proc = await asyncio.create_subprocess_exec(
        *cmd,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE,
    )
    
    async for line in stream_output(proc.stdout):
        yield line.decode().rstrip('\n')
    
    await proc.wait()

async def stream_output(stream):
    """Yield lines from async stream"""
    async for line in stream:
        if line:
            yield line
```

---

## 4. Security: Preventing Command Injection

### ❌ UNSAFE: Shell String Interpolation

```python
# DANGER: Never do this with user input
user_file = "important.txt; rm -rf /"
result = subprocess.run(f"cat {user_file}", shell=True)  # INJECTION!
```

### ✅ SAFE: Use Command List

```python
# Safe: Command is list, no shell interpretation
user_file = "important.txt; rm -rf /"
result = subprocess.run(["cat", user_file])  # Safe - treated as filename
```

### ✅ SAFE: Validate Input

```python
import shlex
from pathlib import Path

def validate_command(cmd: str, allowed_commands: list[str]) -> list[str]:
    """Validate command against allowlist, prevent injection"""
    parts = shlex.split(cmd)
    
    if not parts or parts[0] not in allowed_commands:
        raise ValueError(f"Command '{parts[0]}' not allowed")
    
    # Additional validation: check paths don't escape root
    for part in parts[1:]:
        if ".." in part or part.startswith("/"):
            raise ValueError(f"Path escape attempt: {part}")
    
    return parts

# Usage
cmd = validate_command("ls -la /home/user/files", allowed_commands=["ls", "cat"])
subprocess.run(cmd)
```

---

## 5. Process Lifecycle Management

### Starting and Stopping

```python
import subprocess
import signal
import time

class ProcessManager:
    def __init__(self, cmd: list[str]):
        self.cmd = cmd
        self.process = None
    
    def start(self):
        """Start process in background"""
        self.process = subprocess.Popen(
            self.cmd,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
        )
        return self.process.pid
    
    def stop(self, timeout=5):
        """Stop process gracefully, then kill if needed"""
        if not self.process:
            return
        
        try:
            # Send SIGTERM (graceful shutdown)
            self.process.terminate()
            self.process.wait(timeout=timeout)
        except subprocess.TimeoutExpired:
            # Force kill if process didn't terminate
            self.process.kill()
            self.process.wait()
    
    def is_running(self) -> bool:
        """Check if process is still running"""
        return self.process and self.process.poll() is None
    
    def __enter__(self):
        self.start()
        return self
    
    def __exit__(self, *args):
        self.stop()

# Usage
with ProcessManager(["npm", "run", "dev"]) as pm:
    print(f"Running PID: {pm.process.pid}")
    time.sleep(10)
    # Automatically stops on exit
```

---

## 6. Error Handling & Logging

### Comprehensive Error Handling

```python
import subprocess
import logging

log = logging.getLogger(__name__)

def execute_with_logging(cmd: list[str]) -> tuple[bool, str]:
    """Execute command with comprehensive error handling"""
    log.info(f"Executing: {' '.join(cmd)}")
    
    try:
        result = subprocess.run(
            cmd,
            capture_output=True,
            text=True,
            timeout=30,
            check=True,
        )
        log.info(f"Command succeeded")
        return True, result.stdout
    
    except subprocess.TimeoutExpired:
        log.error(f"Command timed out after 30s")
        return False, "Timeout"
    
    except subprocess.CalledProcessError as e:
        log.error(f"Command failed with code {e.returncode}: {e.stderr}")
        return False, e.stderr
    
    except FileNotFoundError:
        log.error(f"Command not found: {cmd[0]}")
        return False, "Not found"
    
    except Exception as e:
        log.error(f"Unexpected error: {e}", exc_info=True)
        return False, str(e)
```

---

## 7. Approval Workflow (HITL)

### Human-in-the-Loop Approval

```python
class ApprovalWorkflow:
    def __init__(self, callback_approved, callback_rejected):
        self.callback_approved = callback_approved
        self.callback_rejected = callback_rejected
    
    def execute_with_approval(self, cmd: list[str], reason: str) -> bool:
        """Wait for human approval before executing dangerous command"""
        
        # Show pending command to user
        print(f"COMMAND PENDING: {' '.join(cmd)}")
        print(f"Reason: {reason}")
        
        # Wait for approval (could be async event/webhook/API)
        approved = self.wait_for_approval(timeout=300)  # 5 min timeout
        
        if not approved:
            self.callback_rejected(cmd, reason)
            return False
        
        # Execute approved command
        result = subprocess.run(cmd, capture_output=True)
        self.callback_approved(cmd, result)
        return result.returncode == 0
    
    def wait_for_approval(self, timeout):
        """Override in subclass to implement approval mechanism"""
        # Could be: webhook endpoint, message queue, CLI prompt, etc.
        raise NotImplementedError
```

---

## 8. Context Managers for Resource Cleanup

```python
class CommandContext:
    """Context manager for command execution with cleanup"""
    
    def __init__(self, cmd: list[str]):
        self.cmd = cmd
        self.process = None
        self.output = []
    
    def __enter__(self):
        self.process = subprocess.Popen(
            self.cmd,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True,
        )
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        # Cleanup: ensure process is killed
        if self.process and self.process.poll() is None:
            self.process.kill()
        return False  # Don't suppress exceptions
    
    def wait(self, timeout=None):
        return self.process.wait(timeout=timeout)

# Usage
try:
    with CommandContext(["python", "-m", "http.server"]) as ctx:
        time.sleep(5)
except Exception as e:
    print(f"Error: {e}")
# Process automatically killed here
```

---

## Best Practices Summary

| Scenario | Pattern | Key Points |
|----------|---------|-----------|
| Simple one-off command | `subprocess.run()` | Use list, not shell string |
| Long-running with feedback | Stream async with `create_subprocess_exec()` | Yield lines, don't block UI |
| Background process (start/stop) | `ProcessManager` context manager | Graceful shutdown, timeout kill |
| User approval required | Approval workflow queue | Timeout, audit logging |
| Dangerous commands | Allowlist validation + audit | Log all attempts, prevent injection |
| Error handling | Structured logging + exceptions | Distinguish timeout/error/not-found |

---

## See Also

- Python subprocess docs: https://docs.python.org/3/library/subprocess.html
- asyncio docs: https://docs.python.org/3/library/asyncio.html
- Project-specific implementations (see your project documentation)

