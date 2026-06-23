# moss-google

Cryptographic signing for Google GenAI SDK (Gemini) outputs using ML-DSA-44 post-quantum signatures.

[![PyPI](https://img.shields.io/pypi/v/moss-google)](https://pypi.org/project/moss-google/)
[![License](https://img.shields.io/badge/license-BSL--1.1-blue)](LICENSE)

## Overview

moss-google integrates MOSS cryptographic signing into your Google GenAI SDK usage. Every function call, response, and content block gets a tamper-evident signature using ML-DSA-44 (NIST FIPS 204), the post-quantum cryptographic standard. This creates an immutable audit trail for compliance, debugging, and accountability.

## Installation

```bash
pip install moss-google
```

## Quick Start

```python
import google.generativeai as genai
from moss_google import sign_function_call, sign_response

genai.configure(api_key="...")
model = genai.GenerativeModel("gemini-pro")

response = model.generate_content(
    "What's the weather in NYC?",
    tools=[get_weather_tool]
)

# Sign the full response
result = sign_response(response, agent_id="weather-bot")
print(f"Signed: {result.signature[:20]}...")
```

## Features

- **ML-DSA-44 signatures** - Post-quantum cryptographic standard (NIST FIPS 204)
- **Function call signing** - Sign every Gemini function call
- **Response signing** - Sign GenerateContentResponse objects
- **Content signing** - Sign Content and Part objects
- **Policy enforcement** - Block high-risk actions with enterprise policies
- **Offline verification** - Verify signatures without network access

## Usage Examples

### Basic Usage

```python
from moss_google import sign_function_call, sign_response, verify_envelope

# Sign individual function calls
for part in response.candidates[0].content.parts:
    if hasattr(part, "function_call"):
        result = sign_function_call(part.function_call, agent_id="my-bot")

# Verify any envelope
verify_result = verify_envelope(result.envelope)
print(f"Valid: {verify_result.valid}, Subject: {verify_result.subject}")
```

### With Policy Enforcement

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_google import sign_function_call

result = sign_function_call(
    function_call,
    agent_id="finance-bot",
    context={"user_id": "u123"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## API Reference

| Function | Description |
|----------|-------------|
| `sign_function_call()` | Sign a function call |
| `sign_function_call_async()` | Async version |
| `sign_response()` | Sign a GenerateContentResponse |
| `sign_response_async()` | Async version |
| `sign_content()` | Sign a Content object |
| `sign_content_async()` | Async version |
| `sign_part()` | Sign a Part (text or function call) |
| `sign_part_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |
| `enterprise_enabled()` | Check if enterprise mode is active |

## Configuration

| Environment Variable | Description |
|---------------------|-------------|
| `MOSS_API_KEY` | API key for enterprise features (policy enforcement, SIEM) |
| `MOSS_API_URL` | Custom API endpoint (default: api.mosscomputing.com) |

## Links

- [Documentation](https://docs.mosscomputing.com/sdks/google)
- [Dashboard](https://app.mosscomputing.com)
- [PyPI](https://pypi.org/project/moss-google/)

## License

Business Source License 1.1 - Production use requires a [MOSS subscription](https://mosscomputing.com/pricing).
