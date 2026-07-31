# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_secrets` | $0.02 | Regex scan across all 8 categories (S1–S8); any finding → `NO_COMMIT` |

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
result = dcl_evaluate_secrets(
    response=agent_output,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    block_and_alert(result["findings"])
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
      "type": "api_key",
      "provider": "openai",
      "position": 87,
      "redacted_sample": "sk****************3456",
      "severity": "critical",
      "category": "S1"
    }
  ],
  "detection_count": 0,
  "categories_checked": ["S1","S2","S3","S4","S5","S6","S7","S8"],
  "categories_clear": ["S1","S2","S3","S4","S5","S6","S7","S8"],
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "timestamp": 0.0,
  "seal_text": "Verified by Leibniz Layer | Fronesis Labs — Base Mainnet",
  "verify_url": "https://x402.fronesislabs.com/verify/<hash>"
}
```

Only `input_hash` (a hash of the scanned text) and finding metadata are
written to the audit chain — raw text and any real secret values are never
stored. `redacted_sample` shows only the first 2 and last 4 characters of
any match.
