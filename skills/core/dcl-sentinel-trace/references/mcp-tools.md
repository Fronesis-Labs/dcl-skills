# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_pii` | $0.02 | Regex scan across all 8 categories (T1–T8); any finding → `NO_COMMIT` |

## Connecting to the live server

```json
{
  "mcpServers": {
    "dcl-trust-oracle": {
      "url": "https://mcp.fronesislabs.com/mcp"
    }
  }
}
```

Payment is handled automatically for x402-capable clients; clients without
native x402 support fall back to a guided payment flow. No API key or
account signup is required — only a wallet capable of paying in USDC on
Base. Prices are set server-side and may change; the MCP tool description
returned by the server at call time is the source of truth.

### Calling the tool

```python
result = dcl_evaluate_pii(
    response=agent_output,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    redact_and_reprocess(result["findings"])
else:
    log_audit(result["tx_hash"])
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "risk_score": 0.0,
  "findings": [
    {
      "type": "email",
      "position": 14,
      "redacted_sample": "jo****doe.com",
      "severity": "major",
      "category": "T1"
    }
  ],
  "detection_count": 0,
  "categories_checked": ["T1","T2","T3","T4","T5","T6","T7","T8"],
  "categories_clear": ["T1","T2","T3","T4","T5","T6","T7","T8"],
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "timestamp": 0.0,
  "seal_text": "Verified by Leibniz Layer | Fronesis Labs — Base Mainnet",
  "verify_url": "https://x402.fronesislabs.com/verify/<hash>"
}
```

Only `input_hash` (a hash of the scanned text) and finding metadata are
written to the audit chain — raw text and any real personal data are never
stored. `redacted_sample` shows only the first 2 and last 4 characters of
any match.
