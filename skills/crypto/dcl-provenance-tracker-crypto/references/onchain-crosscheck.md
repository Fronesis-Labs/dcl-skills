# Optional: cross-check a past scan's on-chain integrity

Same model as `dcl-provenance-tracker`'s cross-check: the diff itself is
local and free; this is an optional verification of an *existing* prior
record, not a version-diff tool.

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_audit_decode` | $0.10 | Retrieve a past record by `tx_hash` |
| `dcl_audit_decode_deep` | $0.50 | Same, plus full chain-integrity verification and drift context |

```json
{
  "mcpServers": {
    "dcl-trust-oracle": {
      "url": "https://mcp.fronesislabs.com/mcp"
    }
  }
}
```

## Integration patterns

### Update gate (recommended)

```
skill / smart-contract-integration update available
        │
        ▼
DCL Provenance Tracker Crypto ──► BLOCK? → Refuse update, show findings
        │ PASS / WARN
        ▼
Apply update (WARN: show findings to user first)
```

### CI/CD pipeline

```python
for update in pending_contract_integration_updates:
    result = dcl_provenance_tracker_crypto(baseline, candidate)
    if result["verdict"] == "BLOCK":
        reject(update, result["findings"])
    elif result["verdict"] == "WARN":
        flag_for_human_review(update, result)
    else:
        approve(update)
```
