# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_jailbreak_crypto` | $0.02 | Crypto-specialized instruction-override / jailbreak / injection detection, including drain-wallet and unlimited-approval injection patterns |

Prices are set server-side and may change; the MCP tool description
returned by the server at call time is the source of truth.

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
result = dcl_evaluate_jailbreak_crypto(
    response=incoming_prompt,
    agent_id="my-trading-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    block_or_reject(result["reason"])
else:
    log_audit(result["tx_hash"])
    forward_to_model(incoming_prompt)
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "reason": "string",
  "findings": [
    {
      "type": "role_switch | instruction_override | token_smuggling | drain_wallet_injection | unlimited_approval_injection",
      "severity": "critical | major"
    }
  ],
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "policy_version": "string"
}
```

Only `input_hash` (a hash of the screened text) is stored — the raw input
itself is never persisted server-side.
