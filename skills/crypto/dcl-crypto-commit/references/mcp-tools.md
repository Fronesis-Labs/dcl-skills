# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_commit` | $0.01 | Writes a trading/agent decision to the append-only Leibniz Layer audit chain and returns a Merkle-proof receipt |

Prices are set server-side and may change; the MCP tool description
returned by the server at call time is the source of truth.

There is no meaningful free equivalent for this skill: the whole point is
the on-chain, tamper-evident, append-only commit. A locally-computed hash
with no chain anchoring does not provide the same guarantee — see
`references/local-fingerprint-caveat.md` if you need something for
fully-offline use anyway.

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
result = dcl_commit(
    decision=trade_decision_text,
    agent_id="my-trading-agent-01",
    prior_checks={
        "prompt_firewall_tx_hash": "...",
        "trade_verifier_tx_hash": "...",
        "mev_compliance_tx_hash": "...",
    },
)

log_receipt(result["tx_hash"], result["chain_hash"], result["chain_depth"])
```

Passing the `tx_hash`es from earlier pipeline steps is optional but
recommended — it links this commit to the specific checks that passed
before it, in one auditable record.

### Output shape

```json
{
  "tx_hash": "string",
  "chain_hash": "string",
  "chain_depth": 0,
  "input_hash": "string",
  "timestamp": 0.0
}
```

`tx_hash` is the tamper-evident proof of this specific commit. `chain_hash`
links to the previous commit in the append-only chain. `chain_depth` is
this commit's position in the chain.
