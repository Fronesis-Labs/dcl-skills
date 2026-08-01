# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_signal` | $0.03 | Flags fabricated prices, invented token names, contradicted indicators, and overconfident/certain-outcome language in market signal output |

Prices are set server-side and may change; the MCP tool description
returned by the server at call time is the source of truth.

This is a pattern-based heuristic on the output text alone — for a full
claim-by-claim check against a specific source document (e.g. a price feed
snapshot), use the local grounding workflow in
`references/grounding-workflow.md` instead, mirroring how
`dcl-semantic-drift-guard` separates its live pre-check from its full local
workflow.

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
result = dcl_evaluate_signal(
    response=signal_output,
    agent_id="my-signal-provider-01",
)

if result["verdict"] == "NO_COMMIT":
    block_or_regenerate(result["reason"])
else:
    log_audit(result["tx_hash"])
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "findings": [
    {
      "type": "guaranteed_price_prediction | absolute_certainty_claim | fabricated_price | invented_token",
      "severity": "critical | major"
    }
  ],
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "timestamp": 0.0
}
```

Only `input_hash` and finding metadata are written to the audit chain — raw
text never stored server-side.
