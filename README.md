# eda_ai_assist
AI assisted Shell, aka "Ash". Wraps around your existing shell and brings AI-LLM to the CLI for analyzing EDA files.

# Ash CLI Assistant

Ash is a command‑line AI assistant designed for hardware engineers who work with large EDA artifacts. It wraps around your existing shell and brings modern AI/LLM capabilities directly into Linux/UNIX terminals and Windows PowerShell. Ash emphasizes clarity, predictability, and operational safety — with strict control over credentials, logging, and multi‑user behavior.

## Features
- Simple CLI interface for sending prompts to AI models
- Supports multiple credential delivery paths
- Secure encrypted token format for multi-user environments
- Raw API key support for single-user setups
- Provider abstraction (currently Gemini)
- Structured usage logging and per-user accounting
- Predictable, auditable behavior

## Installation
Clone the repository and install dependencies:

```
python3 -m pip install google-genai
```

Ensure the `google-genai` package is installed for Gemini support.

## API Key Configuration
Ash supports three ways to provide credentials. They are evaluated in the following order:

### 1. ASH_GEMINI_TOKEN (Encrypted Token)
A secure, multi-user-safe encrypted token. This token is bound to a username and protected with an HMAC signature. It requires a site secret key to decrypt.

Environment variable:
```
export ASH_GEMINI_TOKEN="username|cipher|signature"
```

### 2. ASH_GEMINI_API_KEY (Raw API Key)
A simple raw API key for single-user environments. Overrides the encrypted token if both are present.

```
export ASH_GEMINI_API_KEY="your-api-key"
```

### 3. GEMINI_API_KEY (SDK Fallback)
If neither Ash-specific variable is set, the Gemini SDK will fall back to its standard environment variable:

```
export GEMINI_API_KEY="your-api-key"
```

### Provider Selection
Ash defaults to Gemini but is designed to support additional providers such as Azure OpenAI in the future:

```
export ASH_PROVIDER="gemini"
```

## Usage
Run Ash with a prompt:

```
ash Explain clock domain crossing
```
```
ash Summarize the worst setup timing violations in file post_route_timing.rpt
```

Run Ash in a build script:

```
ash "analyze simulation1.vcd and find when in time the signal fault_int asserts. Output to file results.txt"
```

Run Ash as a shell wrapper:

```
[ash]:/home/khubbard% summarize file top.v
```

Using Ash as a Python API:

```
from eda_ai_assist import api_eda_ai_assist

ash = api_eda_ai_assist()
result = ash.ask_ai("Summarize this file top.sdc")
print(result)
```


On each prompt, Ash will load provider configuration, resolve credentials, and send the request to the selected model.

## Security Model
Ash is designed for environments where multiple users may share a system. Key security features include:

- Encrypted tokens bound to usernames
- HMAC integrity verification
- Site secret key required for decryption
- Raw API key override for single-user setups
- Clear error handling when credentials are missing or invalid

## Logging
Ash maintains two logs:

### usage_query.log
Append-only log of all prompts sent to the AI. Safe under concurrent writes.

### usage_totals.log
Tracks per-user upload/download totals. Written atomically to avoid corruption.

## License
This project is released under the GPLv3 license. See `LICENSE` for details.

