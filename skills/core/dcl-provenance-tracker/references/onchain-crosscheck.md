# Optional: cross-check a past scan's on-chain integrity

`dcl_fingerprint` is a **local** proof — this skill has no live endpoint of
its own, and nothing is submitted anywhere by default. If you've *also* run
one of the other DCL Skills' live tools (e.g. `dcl_evaluate_safety` from DCL
Skill Auditor) against either version and logged its `tx_hash`, you can
separately verify that on-chain record hasn't been tampered with since:

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_audit_decode` | $0.10 | Retrieve a past record by `tx_hash` |
| `dcl_audit_decode_deep` | $0.50 | Same, plus full chain-integrity verification |

This is a **verification of an existing prior record**, not a version-diff
tool — it doesn't compare two skill versions. Use it alongside this skill's
own diff, not instead of it.

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
skill update available
        │
        ▼
DCL Provenance Tracker ──► BLOCK? → Refuse update, show findings
        │ PASS / WARN
        ▼
Apply update (WARN: show findings to user first)
```

### Full DCL Security Suite pipeline

```
New skill / update detected
        │
        ▼
DCL Skill Auditor          ← is the skill itself safe to install?
        │ PASS
        ▼
DCL Provenance Tracker     ← did this update introduce new risks?
        │ PASS
        ▼
DCL Policy Enforcer        ← does skill output comply with policies?
        │ COMMIT
        ▼
DCL Sentinel Trace         ← does output expose PII?
        │ COMMIT
        ▼
DCL Semantic Drift Guard   ← is output grounded in source?
        │ IN_COMMIT
        ▼
Safe to deliver
```
