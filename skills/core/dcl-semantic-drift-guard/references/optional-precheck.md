# Optional faster pre-check via live paid service

⚠️ This sends data over the network. Calling this tool transmits the
`llm_output` text to Fronesis Labs' MCP server and writes a hash of it plus
verdict metadata to an on-chain audit trail. Do not use this with
confidential, regulated, or sensitive text unless the user has confirmed
that's acceptable. Entirely optional — the free workflow never leaves the
agent.

Useful as a quick heuristic signal before running the full source-grounding
workflow, or as a cheap secondary check for overconfidence and
fabrication-prone language on its own, without a source document.

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_quality` | $0.03 | Flags overconfident/absolute-claim language patterns and produces an on-chain `tx_hash` |

This is a pattern-based heuristic on the output text alone — it does **not**
take a source document and does not perform the claim-by-claim grounding
check this skill does. Useful as a fast first-pass filter or an independent,
cryptographically-anchored confirmation alongside this skill's own
`tx_hash`, not as a replacement for the full workflow.

```json
{
  "mcpServers": {
    "dcl-trust-oracle": {
      "url": "https://mcp.fronesislabs.com/mcp"
    }
  }
}
```

No API key or account signup required — only a wallet capable of paying in
USDC on Base. Prices are set server-side and may change; the MCP tool
description returned by the server at call time is the source of truth.

## Integration patterns

### With DCL Policy Enforcer (recommended pipeline)

```
LLM Output
    │
    ▼
DCL Policy Enforcer ──► NO_COMMIT? → Block immediately
    │ COMMIT
    ▼
DCL Semantic Drift Guard ──► HALLUCINATION_DRIFT? → Block / re-generate
    │ IN_COMMIT
    ▼
Safe to deliver
```

### With DCL Sentinel Trace (full stack)

```
Sentinel Trace → strip PII before source reaches LLM
Policy Enforcer → policy check on output
Semantic Drift Guard → factual grounding check
```

### Standalone (quick RAG validation)

```python
result = dcl_semantic_drift_guard(
    source_mode="kb_query",
    kb_endpoint="https://kb.yourapp.com/query",
    kb_query="penalty clauses breach of contract",
    llm_output=agent_response,
    strictness="strict",
)

if result["data"]["verdict"] == "HALLUCINATION_DRIFT":
    raise ValueError(f"Drift detected: {result['data']['drift_items']}")
```
