---
layout: default
title: Python SDK
permalink: /python-sdk/
---

# Python SDK Documentation

Official Python SDK for the BugBountyKE-KSP Platform API.

## 📦 Installation

### From PyPI (Recommended)

```bash
pip install bugbounty-ksp-api-client
```

**Current Version:** `1.0.0` [View on PyPI](https://pypi.org/project/bugbounty-ksp-api-client/)

### From Source

```bash
git clone https://github.com/BugBounty-MockingBird/bugbounty-ksp-api-client.git
cd bugbounty-ksp-api-client
pip install -e .
```

### Verify Installation

```python
from bugbounty_ksp import BugBountyKSPAPIClient
from bugbounty_ksp.api import generate_api_key
print("SDK installed successfully!")
```

## 🔑 API Key Management

### Generating API Keys

The SDK includes a built-in secure API key generator:

```python
from bugbounty_ksp.api import generate_api_key, APIKeyGenerator

# Generate a test API key (sk_test_*)
test_key = generate_api_key()
print(test_key)  # Output: sk_test_aBcDeFgHiJkLmNoPqRsTuVwXyZ123456

# Generate production key (sk_*)
prod_key = APIKeyGenerator.generate(test=False)

# Generate multiple keys at once
keys = generate_api_keys(count=5)  # Returns list of 5 test keys
```

### Validating Keys

```python
from bugbounty_ksp.api.key_generator import APIKeyGenerator

key = "sk_test_your_key_here"

# Check if key format is valid
is_valid = APIKeyGenerator.is_valid_format(key)  # Returns: True/False

# Get key information
info = APIKeyGenerator.get_key_info(key)
print(info)
# Output:
# {
#   'is_valid': True,
#   'length': 41,
#   'masked': 'sk_test_****...****abcd',
#   'environment': 'test',
#   'type': 'secret_key',
#   'prefix': 'sk_',
#   'created_at': '2026-02-05T...'
# }
```

### Safe Logging with Key Masking

```python
from bugbounty_ksp.api.key_generator import APIKeyGenerator

key = "sk_test_aBcDeFgHiJkLmNoPqRsTuVwXyZ123456"

# Mask key for safe logging (shows only last 4 chars)
masked = APIKeyGenerator.mask_key(key, visible_chars=4)
print(f"Using key: {masked}")  # Using key: sk_test_****...6
```

## 🚀 Quick Start

### 1. Initialize Client

```python
from bugbounty_ksp import BugBountyKSPAPIClient
import os

# Get API key from environment
api_key = os.environ.get('BUGBOUNTY_API_KEY', 'sk_test_your_key')

# Create client
client = BugBountyKSPAPIClient(api_key=api_key)
```

### 2. Publish Article

```python
response = client.publish_article(
    title="Security Vulnerability Report",
    content="""# Vulnerability Details

## Overview
This is a detailed security vulnerability report.

## Impact
High impact vulnerability affecting authentication systems.

## Remediation
Follow these steps to fix the issue...
""",
    frontmatter={
        "title": "Security Vulnerability Report",
        "tags": ["security", "bug-bounty", "authentication"],
        "category": "web",
        "author": "Security Researcher",
        "date": "2026-02-05"
    },
    file_path="/path/to/article.md"
)

print(f"Article published!")
print(f"Web URL: {response.web_url}")
print(f"Article ID: {response.article_id}")
```

### 3. Delete Article

```python
try:
    response = client.delete_article(article_id="article_123")
    print("Article deleted successfully")
except Exception as e:
    print(f"Error deleting article: {e}")
```

## ⚙️ Configuration

### Environment Variables

```bash
# Required
export BUGBOUNTY_API_KEY="sk_test_your_key"

# Optional
export BUGBOUNTY_API_URL="https://api.bugbounty-ksp.com"
export BUGBOUNTY_VERIFY_SSL="true"
export BUGBOUNTY_TIMEOUT="30"
```

### Custom Configuration

```python
client = BugBountyKSPAPIClient(
    api_key="sk_test_...",
    api_url="https://staging-api.bugbounty-ksp.com",  # Custom endpoint
    verify_ssl=False,  # Disable SSL for testing
    timeout=60  # Longer timeout for large uploads
)
```

### Custom Headers

```python
client = BugBountyKSPAPIClient(
    api_key="sk_test_...",
    custom_headers={
        "X-Custom-Header": "value",
        "X-Request-ID": "12345"
    }
)
```

## 🔐 Authentication

The SDK automatically handles authentication using API keys. All requests include the key in the Authorization header:

```
Authorization: Bearer sk_test_your_key_here
```

### Authentication Verification

```python
try:
    # This is called automatically during client initialization
    client._verify_authentication()
    print("Authentication successful!")
except AuthenticationError as e:
    print(f"Authentication failed: {e}")
```

## 📊 API Reference

### Core Classes

#### `BugBountyKSPAPIClient`

Main client for interacting with the API.

```python
client = BugBountyKSPAPIClient(
    api_key,           # Required: API key (str)
    api_url=None,      # Optional: Custom API URL
    verify_ssl=True,   # Optional: SSL verification
    timeout=30,        # Optional: Request timeout (seconds)
    custom_headers={}  # Optional: Additional headers
)
```

**Methods:**

| Method | Description | Return |
|--------|-------------|--------|
| `publish_article(title, content, frontmatter, file_path)` | Publish new article | `PublishResponse` |
| `delete_article(article_id)` | Delete article | `DeleteResponse` |
| `_verify_authentication()` | Verify API key | `None` (raises on failure) |

#### `APIKeyGenerator`

Secure API key generation and management.

```python
from bugbounty_ksp.api.key_generator import APIKeyGenerator

# Static methods (no instance needed)
APIKeyGenerator.generate(test=True)           # Generate single key
APIKeyGenerator.generate_batch(count=5)       # Generate multiple keys
APIKeyGenerator.is_valid_format(key)          # Validate format
APIKeyGenerator.mask_key(key, visible_chars=4)  # Mask for logging
APIKeyGenerator.get_key_info(key)             # Get key metadata
```

### Data Models

#### `PublishResponse`

Response from publishing an article.

```python
{
    "success": bool,
    "article_id": str,
    "title": str,
    "web_url": str,
    "published_at": str,
    "tags": List[str],
    "category": str
}
```

#### `DeleteResponse`

Response from deleting an article.

```python
{
    "success": bool,
    "article_id": str,
    "message": str
}
```

### Exception Classes

```python
from bugbounty_ksp.api.exceptions import (
    APIError,              # Base exception
    AuthenticationError,   # Invalid/missing API key
    ValidationError,       # Invalid input data
    RateLimitError,       # Too many requests (429)
    NotFoundError,        # Resource not found (404)
    NetworkError,         # Network/connection issues
)
```

## ⚠️ Error Handling

```python
from bugbounty_ksp import BugBountyKSPAPIClient
from bugbounty_ksp.api.exceptions import (
    ValidationError,
    AuthenticationError,
    RateLimitError,
    NetworkError,
)

client = BugBountyKSPAPIClient(api_key="sk_test_...")

try:
    response = client.publish_article(
        title="",  # Invalid: empty title
        content="Test"
    )
except ValidationError as e:
    print(f"Validation error: {e}")
    # Handle validation errors

except AuthenticationError as e:
    print(f"Authentication failed: {e}")
    # Check or regenerate API key

except RateLimitError as e:
    print(f"Rate limited: {e}")
    # Wait before retrying

except NetworkError as e:
    print(f"Network error: {e}")
    # Handle connection issues
```

## 🔄 Context Manager Pattern

Use the client as a context manager for automatic cleanup:

```python
from bugbounty_ksp import BugBountyKSPAPIClient

with BugBountyKSPAPIClient(api_key="sk_test_...") as client:
    response = client.publish_article(
        title="My Article",
        content="Content here"
    )
    print(response.web_url)
# Session automatically closed
```

## 📚 Complete Example

```python
from bugbounty_ksp import BugBountyKSPAPIClient
from bugbounty_ksp.api import generate_api_key, APIKeyGenerator
from bugbounty_ksp.api.exceptions import APIError
import os

def main():
    # 1. Generate or retrieve API key
    api_key = os.environ.get('BUGBOUNTY_API_KEY')
    if not api_key:
        print("No API key found. Generating test key...")
        api_key = generate_api_key()
        print(f"Test key: {APIKeyGenerator.mask_key(api_key)}")
    
    # 2. Initialize client
    client = BugBountyKSPAPIClient(
        api_key=api_key,
        timeout=30
    )
    
    # 3. Prepare article
    article_content = """# SQL Injection Vulnerability Report

## Vulnerability Description
Found SQL injection vulnerability in user authentication endpoint.

## Steps to Reproduce
1. Navigate to login page
2. Enter `' OR '1'='1` in username field
3. Observe database error message

## Impact
- Unauthorized database access
- User data exposure
- Account takeover

## Remediation
Use parameterized queries and input validation.
"""
    
    # 4. Publish article
    try:
        response = client.publish_article(
            title="SQL Injection in Auth Endpoint",
            content=article_content,
            frontmatter={
                "tags": ["security", "sql-injection", "authentication"],
                "category": "web",
                "severity": "high"
            },
            file_path="/tmp/article.md"
        )
        
        print(f"✅ Published successfully!")
        print(f"   Article ID: {response.article_id}")
        print(f"   View at: {response.web_url}")
        
    except APIError as e:
        print(f"❌ Error publishing article: {e}")
        return False
    
    return True

if __name__ == "__main__":
    success = main()
    exit(0 if success else 1)
```

## 🧪 Testing

The SDK includes comprehensive tests. Run them locally:

```bash
# Install dev dependencies
pip install -e ".[dev]"

# Run all tests
pytest tests/ -v

# Run specific test
pytest tests/test.py::TestAPIKeyGenerator -v

# Run with coverage
pytest tests/ --cov=bugbounty_ksp --cov-report=html
```

**Test Coverage:**
- ✅ 33 tests total
- ✅ 100% code coverage
- ✅ API client initialization
- ✅ Authentication & error handling
- ✅ Article publishing
- ✅ API key generation & validation
- ✅ Key masking & metadata extraction

## 📖 Documentation Files

- [README on GitHub](https://github.com/BugBounty-MockingBird/bugbounty-ksp-api-client)
- [PyPI Package](https://pypi.org/project/bugbounty-ksp-api-client/)
- [GitHub Repository](https://github.com/BugBounty-MockingBird/bugbounty-ksp-api-client)

## 🔗 Related Documentation

- [API Reference](api-reference.md) - Full REST API documentation
- [Architecture Guide](architecture.md) - System design overview
- [Getting Started](getting-started.md) - Platform setup guide
- [Deployment Guide](deployment.md) - Production deployment instructions

## 💬 Support

- 📦 **PyPI:** https://pypi.org/project/bugbounty-ksp-api-client/
- 🐛 **Issues:** https://github.com/BugBounty-MockingBird/bugbounty-ksp-api-client/issues
- 📧 **Email:** team@bugbounty-ksp.com

## 📝 License

MIT License - [View License](../LICENSE)

---

**Last Updated:** February 5, 2026  
**SDK Version:** 1.0.0  
**Maintained by:** BugBountyKE-KSP Team
