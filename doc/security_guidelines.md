# Security Guidelines for Applications

**Status:** Reference guide for security best practices  
**Last Updated:** 2026-10-20  
**Scope:** Cross-project - adapt for your specific application

## Overview

This guide covers common security patterns for Python applications handling file operations, configuration, and external integrations.

---

## 1. File System Security

### Path Traversal Prevention

```python
from pathlib import Path

class PathValidator:
    def __init__(self, allowed_root: str):
        self.allowed_root = Path(allowed_root).resolve()
    
    def validate(self, user_path: str) -> Path:
        """Validate user path doesn't escape allowed root"""
        # Resolve to absolute path
        requested = (self.allowed_root / user_path).resolve()
        
        # Check it's still under root
        try:
            requested.relative_to(self.allowed_root)
            return requested
        except ValueError:
            raise ValueError(f"Path escape attempt: {user_path}")

# Usage
validator = PathValidator("/home/user/project")
safe_path = validator.validate("../../../etc/passwd")  # Raises ValueError
safe_path = validator.validate("data/file.txt")  # OK
```

### Symbolic Link Attacks

```python
import os

def is_safe_path(path: str, allowed_root: str) -> bool:
    """Check path is within root and not a symlink escape"""
    root = Path(allowed_root).resolve()
    target = Path(path).resolve()
    
    # Check within root
    try:
        target.relative_to(root)
    except ValueError:
        return False
    
    # Check for dangerous symlinks in path chain
    current = target.parent
    while current != root:
        if current.is_symlink():
            return False  # Symlink in path chain
        current = current.parent
    
    return True
```

### File Permissions

```python
import os
import stat

def set_secure_permissions(path: str):
    """Set secure file permissions (owner read-write only)"""
    os.chmod(path, stat.S_IRUSR | stat.S_IWUSR)  # 600 (rw-------)

def is_secure_file(path: str) -> bool:
    """Check file is not world-readable"""
    mode = os.stat(path).st_mode
    # Check if world-readable
    return not bool(mode & stat.S_IROTH)
```

---

## 2. Configuration & Secrets

### Secret Detection & Filtering

```python
import re

class SensitiveDataFilter:
    """Filter secrets from logs and output"""
    
    PATTERNS = {
        'api_key': re.compile(r'(api[_-]?key|apikey)["\']?[:=]\s*["\']?([a-zA-Z0-9_-]{20,})'),
        'password': re.compile(r'(password)["\']?[:=]\s*["\']?([^\s"\']+)'),
        'token': re.compile(r'(token|auth)["\']?[:=]\s*["\']?([a-zA-Z0-9_.-]{20,})'),
        'aws_key': re.compile(r'AKIA[0-9A-Z]{16}'),
        'url_with_creds': re.compile(r'(https?://)[^:]+:[^@]+@'),
    }
    
    @classmethod
    def redact(cls, text: str) -> str:
        """Redact secrets from text"""
        for secret_type, pattern in cls.PATTERNS.items():
            text = pattern.sub(r'\1=***REDACTED***', text)
        return text
    
    @classmethod
    def is_likely_secret(cls, value: str) -> bool:
        """Check if value looks like a secret"""
        if len(value) < 10:
            return False
        return any(pattern.search(value) for pattern in cls.PATTERNS.values())

# Usage
filter = SensitiveDataFilter()
log_line = "API_KEY=sk-1234567890abcdef"
safe = filter.redact(log_line)  # "API_KEY=***REDACTED***"
```

### Environment Variables Safely

```python
import os
from pathlib import Path

class SecureConfig:
    """Load config from environment, validate, and redact secrets"""
    
    def __init__(self):
        self.config = {}
    
    def load_from_env(self, required_keys: list[str]):
        """Load config from environment variables"""
        for key in required_keys:
            if key not in os.environ:
                raise ValueError(f"Required env var missing: {key}")
            self.config[key] = os.environ[key]
    
    def load_from_file(self, path: str, required_keys: list[str]):
        """Load from .env file (not committed to git)"""
        with open(path) as f:
            for line in f:
                if '=' in line and not line.startswith('#'):
                    key, value = line.strip().split('=', 1)
                    self.config[key] = value
        
        for key in required_keys:
            if key not in self.config:
                raise ValueError(f"Missing key in config file: {key}")
    
    def __repr__(self):
        """Redact secrets in repr"""
        redacted = {}
        for k, v in self.config.items():
            if any(secret in k.lower() for secret in ['key', 'token', 'password']):
                redacted[k] = '***REDACTED***'
            else:
                redacted[k] = v
        return f"SecureConfig({redacted})"

# Usage
config = SecureConfig()
config.load_from_file('.env', required_keys=['API_KEY', 'DATABASE_URL'])
print(config)  # Shows ***REDACTED*** for secrets
```

### No Secrets in Code

```python
# ❌ WRONG: Never commit secrets
CONFIG = {
    "api_key": "sk-1234567890abcdef",
    "database_password": "super_secret_123",
}

# ✅ RIGHT: Load from environment
import os
CONFIG = {
    "api_key": os.environ["API_KEY"],  # Set in .env or CI/CD
    "database_password": os.environ["DATABASE_PASSWORD"],
}

# ✅ BETTER: Use a .env file (add to .gitignore)
from dotenv import load_dotenv
load_dotenv()
CONFIG = {
    "api_key": os.environ["API_KEY"],
}
```

---

## 3. Input Validation

### Command Injection Prevention

```python
import shlex
from typing import List

class CommandValidator:
    """Validate user commands against allowlist"""
    
    ALLOWED_COMMANDS = {
        'ls': ['ls', 'ls -la', 'ls -lh'],
        'cat': ['cat'],
        'grep': ['grep'],
        'find': ['find'],
    }
    
    @classmethod
    def validate(cls, user_cmd: str) -> List[str]:
        """Parse and validate command"""
        try:
            parts = shlex.split(user_cmd)
        except ValueError as e:
            raise ValueError(f"Invalid command syntax: {e}")
        
        if not parts:
            raise ValueError("Empty command")
        
        base_cmd = parts[0]
        if base_cmd not in cls.ALLOWED_COMMANDS:
            raise ValueError(f"Command not allowed: {base_cmd}")
        
        # Additional argument validation per command
        if base_cmd == 'find':
            if any(arg.startswith('-exec') for arg in parts):
                raise ValueError("find -exec not allowed")
        
        return parts
```

### SQL Injection Prevention

```python
# ❌ WRONG: String interpolation
query = f"SELECT * FROM users WHERE id = {user_id}"

# ✅ RIGHT: Parameterized queries
import sqlite3
conn = sqlite3.connect('db.sqlite3')
cursor = conn.cursor()
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

### XSS Prevention (Web)

```python
from html import escape

# ❌ WRONG: Direct HTML output
user_input = "<script>alert('xss')</script>"
html = f"<div>{user_input}</div>"  # Executes script!

# ✅ RIGHT: Escape HTML
html = f"<div>{escape(user_input)}</div>"  # Shows literal string
```

---

## 4. Authentication & Authorization

### API Key Management

```python
from functools import wraps
import hashlib

class APIKeyManager:
    """Manage API keys securely"""
    
    def __init__(self):
        self.keys = {}  # In production: use database
    
    def generate_key(self, client_id: str) -> str:
        """Generate new API key"""
        import secrets
        key = secrets.token_urlsafe(32)
        # Store hash, not the key itself
        key_hash = hashlib.sha256(key.encode()).hexdigest()
        self.keys[client_id] = key_hash
        return key
    
    def verify_key(self, key: str, client_id: str) -> bool:
        """Verify provided key matches stored hash"""
        key_hash = hashlib.sha256(key.encode()).hexdigest()
        stored = self.keys.get(client_id)
        return stored == key_hash if stored else False

# Flask/FastAPI usage
def require_api_key(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        api_key = request.headers.get('X-API-Key')
        client_id = request.headers.get('X-Client-ID')
        
        if not api_key or not api_key_manager.verify_key(api_key, client_id):
            return {"error": "Unauthorized"}, 401
        
        return f(*args, **kwargs)
    return decorated
```

---

## 5. Logging & Auditing

### Audit Logging

```python
import logging
from datetime import datetime

class AuditLogger:
    """Log security-relevant events"""
    
    def __init__(self, log_file: str):
        self.logger = logging.getLogger('audit')
        handler = logging.FileHandler(log_file)
        self.logger.addHandler(handler)
    
    def log_access(self, user: str, resource: str, action: str, success: bool):
        """Log file/resource access attempt"""
        status = "ALLOW" if success else "DENY"
        self.logger.info(
            f"[{datetime.now().isoformat()}] {status} | "
            f"user={user} | resource={resource} | action={action}"
        )
    
    def log_auth_attempt(self, user: str, success: bool, ip: str):
        """Log authentication attempt"""
        status = "SUCCESS" if success else "FAILED"
        self.logger.info(
            f"[{datetime.now().isoformat()}] AUTH {status} | "
            f"user={user} | ip={ip}"
        )

# Usage
audit = AuditLogger('/var/log/myapp/audit.log')
audit.log_auth_attempt('alice', True, '192.168.1.100')
audit.log_access('alice', '/data/secrets', 'read', False)  # Denied
```

### Never Log Sensitive Data

```python
import logging

log = logging.getLogger(__name__)

# ❌ WRONG
log.info(f"Authenticating user with password {password}")

# ✅ RIGHT
log.info(f"Authentication attempt for user {username}")

# For debugging sensitive operations, use audit logger with redaction
from security import SensitiveDataFilter
log_msg = SensitiveDataFilter.redact(debug_output)
audit_log.info(log_msg)
```

---

## 6. HTTPS & TLS

### Force HTTPS in Production

```python
# Flask
from flask_talisman import Talisman

app = Flask(__name__)
Talisman(app, force_https=True)

# FastAPI
from fastapi.middleware import trustedhost

app.add_middleware(
    trustedhost.TrustedHostMiddleware,
    allowed_hosts=["example.com", "www.example.com"],
)
```

### Certificate Verification

```python
import requests

# ❌ WRONG: Ignore SSL errors (never in production!)
requests.get("https://api.example.com", verify=False)

# ✅ RIGHT: Always verify certificates
requests.get("https://api.example.com", verify=True)

# Custom CA certificate
requests.get(
    "https://api.example.com",
    verify="/path/to/ca-bundle.crt",
)
```

---

## 7. Dependency Security

### Keep Dependencies Updated

```bash
# Check for vulnerable dependencies
pip install safety
safety check

# Or use GitHub Dependabot (automatic PRs)
# Or use Snyk (continuous monitoring)
```

### Pin Versions

```
# ❌ WRONG: Flexible versions allow breaking changes
requests>=2.0.0

# ✅ RIGHT: Pin to specific minor version
requests==2.28.*
```

---

## Security Checklist

- [ ] No hardcoded secrets (use environment variables)
- [ ] Input validation on all user inputs
- [ ] SQL/command injection prevention
- [ ] Path traversal prevention
- [ ] HTTPS enforced in production
- [ ] Authentication required on sensitive endpoints
- [ ] Authorization checked on sensitive resources
- [ ] Audit logging of security events
- [ ] No sensitive data in logs
- [ ] Dependencies up-to-date (no known CVEs)
- [ ] Error messages don't leak system details
- [ ] CORS properly configured (web apps)
- [ ] Rate limiting on APIs
- [ ] Password requirements enforced (if applicable)

---

## See Also

- OWASP Top 10: https://owasp.org/www-project-top-ten/
- CWE/SANS Top 25: https://cwe.mitre.org/top25/
- Project-specific security implementations (see your docs)

