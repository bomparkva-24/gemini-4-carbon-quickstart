# Gemini 4 Carbon Quickstart

Minimal Python examples for text generation, system instructions, JSON responses,
and streaming with Gemini 4 Carbon through CometAPI’s native Gemini-compatible
endpoint.

These examples demonstrate API wiring, not benchmark performance. Model
availability, limits, and pricing can vary by provider and change over time.
```

Corrected setup and basic request example:

```bash
pip install requests
export COMETAPI_KEY="your-key"
```

```python
import os
import requests

BASE = "https://api.cometapi.com/v1beta/models/gemini-4-carbon"

def generate(prompt, *, system_instruction=None, json_mode=False, max_output_tokens=2000):
    api_key = os.environ.get("COMETAPI_KEY")
    if not api_key:
        raise RuntimeError("Set COMETAPI_KEY before running this example")

    body = {
        "contents": [{"role": "user", "parts": [{"text": prompt}]}],
        "generationConfig": {"maxOutputTokens": max_output_tokens},
    }

    if system_instruction:
        body["systemInstruction"] = {"parts": [{"text": system_instruction}]}
    if json_mode:
        body["generationConfig"]["responseMimeType"] = "application/json"

    response = requests.post(
        f"{BASE}:generateContent",
        headers={"x-goog-api-key": api_key},
        json=body,
        timeout=120,
    )
    response.raise_for_status()
    data = response.json()

    candidates = data.get("candidates", [])
    if not candidates:
        raise RuntimeError(f"No candidate returned: {data.get('promptFeedback', {})}")

    parts = candidates[0].get("content", {}).get("parts", [])
    return "".join(part.get("text", "") for part in parts if "text" in part)
```

Also fix the JSON example so the prompt asks for valid JSON matching a defined shape; “list 3 categories as JSON” doesn’t itself guarantee a schema. Keep structured output distinct from merely setting `responseMimeType`.

The requested link is valid in this form:

[Gemini 4 Carbon on CometAPI](https://www.cometapi.com/models/google/gemini-4-carbon/?utm_source=google&utm_medium=github&utm_campaign=bom)
