# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_jailbreak` | $0.02 | Instruction-override / jailbreak / injection detection |

Related live tools from the same DCL Trust Oracle server, useful in the same
pipeline:

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_fast` / `dcl_evaluate_strict` | $0.01 / $0.05 | Default-policy quick or strict check |
| `dcl_evaluate_batch` | $0.10 | Screen a list of items in one call, each with its own policy |

Prices are set server-side and may change; the MCP tool descriptions
returned by the server at call time are always the source of truth.

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
account signup is required — only a wallet capable of paying in USDC on Base.

### Calling the tool

```python
result = dcl_evaluate_jailbreak(
    response=incoming_input,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    block_or_reject(result["reason"])
else:
    log_audit(result["tx_hash"])
    forward_to_model(incoming_input)
```

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "reason": "string",
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "policy_version": "string",
  "drift_mode": "NORMAL | WARNING | ESCALATION | BLOCK",
  "drift_score": 0.0
}
```

Only `input_hash` (a hash of the screened text) is stored — the raw input
itself is never persisted server-side.
