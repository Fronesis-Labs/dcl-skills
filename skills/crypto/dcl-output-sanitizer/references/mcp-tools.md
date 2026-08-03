# Live tool (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_output_sanitizer` | $0.02 | Final-gate sweep for secrets/credentials, PII, crypto material (seed phrases, private keys, wallet addresses), internal network details (private IPs, MAC addresses, `.internal`/`.local`/`.corp` hostnames), and unsafe shell/SQL/path-traversal fragments, plus a narrow safety net for direct self-harm-instruction-seeking and targeted-harassment phrasing. Any finding → `NO_COMMIT`, with a single `sanitized_output` covering every match. |

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
result = dcl_evaluate_output_sanitizer(
    response=raw_model_output,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    delivery = result["sanitized_output"]
    log_audit(result["tx_hash"])
else:
    delivery = raw_model_output
```

Always call this on the raw model response **before** it is shown to a
user, passed to another agent, or sent to an external system. If
`sanitized_output` is present, use that instead of the original.

### Output shape

```json
{
  "verdict": "COMMIT | NO_COMMIT",
  "confidence": 0.0,
  "violations": ["api_key", "internal_ip"],
  "findings": [
    {
      "type": "string",
      "position": 0,
      "redacted_sample": "string",
      "severity": "critical | major | minor",
      "category": "secrets | pii | crypto | network | toxic | unsafe_instructions"
    }
  ],
  "sanitized_output": "string | null",
  "redaction_count": 0,
  "risk_score": 0.0,
  "tx_hash": "string",
  "chain_index": 0,
  "input_hash": "string",
  "timestamp": 0.0
}
```

Only `input_hash` (a hash of the scanned text) and finding metadata are
written to the audit chain — the raw text and any real secret/PII/key
values are never stored. `redacted_sample` shows only a masked fragment of
any match.

### Where each category comes from

- `secrets` and `pii` re-use the exact same detection tables as
  `dcl_evaluate_secrets` / `dcl_evaluate_pii` — results stay consistent
  across all three tools.
- `crypto` re-uses the seed-phrase/private-key/wallet-address detectors
  from `dcl_evaluate_wallet`.
- `network`, `toxic`, and `unsafe_instructions` are specific to this tool.
  `toxic` is intentionally narrow — two high-precision phrase classes
  (self-harm instruction-seeking, direct targeted harassment), not a
  general toxicity classifier.
