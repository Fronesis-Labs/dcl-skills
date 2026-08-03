# Live tools (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_fast` | $0.01 | Quick check against the server's default policy |
| `dcl_evaluate_strict` | $0.05 | Broader **strict** policy — union of default + anti-jailbreak + safety phrases, higher confidence bar |
| `dcl_evaluate_jailbreak` | $0.02 | Instruction-override / jailbreak detection |
| `dcl_evaluate_safety` | $0.01 | Baseline safety check |
| `dcl_evaluate_quality` | $0.03 | Content quality & drift check |
| `dcl_evaluate_batch` | $0.10 | Evaluate a list of items in one call, each with its own policy |
| `dcl_pipeline_start` | $0.05 | Opens a session for a series of checks |
| `dcl_audit_decode` | $0.10 | Retrieve a past record by `tx_hash` |
| `dcl_audit_decode_deep` | $0.50 | Same, plus full chain-integrity verification and drift context |

Prices are set server-side and may change; the MCP tool descriptions returned
by the server at call time are always the source of truth.

> **Note (ClawHub listing):** confirmed against ClawHub's own skill-format
> docs — pricing metadata is disallowed in YAML frontmatter, but documenting
> the external cost of a paid third-party service in the skill's own
> instructions (like this table) is explicitly permitted. No frontmatter
> field carries a price anywhere in this skill, so this table is compliant
> as-is. See `docs/licensing.md` at the repo root for the source citation.

## Note on policy selection

Each tool runs a distinct built-in policy, not just a price tier of the same
check. `dcl_evaluate_fast` and `dcl_evaluate_strict` both use general-purpose
policies but differ in coverage and confidence bar; `dcl_evaluate_jailbreak`,
`dcl_evaluate_safety`, and `dcl_evaluate_quality` are narrower, single-concern
checks. None of the single-item tools take a `policy` parameter — to target a
specific policy per item, use `dcl_evaluate_batch`, where each item may carry
its own `policy` string (`default`, `strict`, `anti_jailbreak`, `safety`, or
`content_quality`).

## Connecting to the live server

Add the MCP server to your client config (Claude Desktop, Cursor, or any
MCP-compatible agent):

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

### Calling a tool

```python
result = dcl_evaluate_jailbreak(
    response=agent_output,
    agent_id="my-agent-01",
)

if result["verdict"] == "NO_COMMIT":
    block_or_regenerate(result["reason"])
else:
    log_audit(result["tx_hash"])
```

### Output shape (all evaluation tools)

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

Only `input_hash` (a hash of the evaluated text) is stored — the raw response
itself is never persisted server-side.
