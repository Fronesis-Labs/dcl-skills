# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_trade` | $0.02 | Screens a trade decision's language for guaranteed-return claims, zero-risk framing, and unqualified buy/sell directives; produces an immutable trade receipt |

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
result = dcl_evaluate_trade(
    response=trade_decision_text,
    agent_id="my-trading-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    block_or_revise(result["reason"])
else:
    log_receipt(result["tx_hash"])
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "reason": "string",
  "findings": [
    {
      "type": "guaranteed_return | zero_risk_claim | unqualified_directive | missing_risk_disclosure",
      "severity": "critical | major"
    }
  ],
  "trade_receipt": {
    "tx_hash": "string",
    "chain_hash": "string",
    "chain_depth": 0
  },
  "input_hash": "string",
  "timestamp": 0.0
}
```

Only `input_hash` and finding metadata are written to the audit chain — the
raw trade text itself is never stored server-side.
