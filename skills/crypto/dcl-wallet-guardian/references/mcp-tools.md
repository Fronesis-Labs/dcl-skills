# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_wallet` | $0.02 | Scan for seed phrases, private keys, wallet addresses, and wallet-context API credentials; any finding → `NO_COMMIT` |

Prices are set server-side and may change; the MCP tool description returned
by the server at call time is the source of truth.

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
Base.

### Calling the tool

```python
result = dcl_evaluate_wallet(
    response=text_to_scan,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    delivery = result["sanitized_output"]
    log_audit(result["tx_hash"])
else:
    delivery = text_to_scan
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "reason": "string",
  "findings": [
    {
      "type": "seed_phrase | private_key | wallet_address | wallet_api_credential",
      "position": 0,
      "redacted_sample": "string",
      "severity": "critical | major"
    }
  ],
  "sanitized_output": "string | null",
  "risk_score": 0.0,
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "policy_version": "string",
  "timestamp": 0.0
}
```

Only `input_hash` (a hash of the scanned text) and finding metadata are
written to the audit chain — the raw text and any real key/seed values are
never stored. `redacted_sample` shows only a masked fragment of any match.
