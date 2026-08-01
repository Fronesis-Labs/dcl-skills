# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_mev` | $0.03 | Screens for front-running/sandwich-attack patterns, wash trading/layering/spoofing, KYC/AML red flags, and pump-and-dump/rug-pull language |

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
result = dcl_evaluate_mev(
    response=agent_output,
    agent_id="my-defi-desk-01",
)

if result["verdict"] == "NO_COMMIT":
    block_and_flag_for_compliance(result["findings"])
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
      "type": "front_running | sandwich_attack | wash_trading | layering | spoofing | kyc_aml_flag | pump_and_dump",
      "severity": "critical | major",
      "regulatory_reference": "EU AI Act Art. 9 | EU AI Act Art. 13 | MiFID II | FCA | null"
    }
  ],
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "timestamp": 0.0
}
```

Only `input_hash` and finding metadata are written to the audit chain — the
raw text is never stored server-side. `tx_hash` is intended for regulatory
audit trail purposes.
