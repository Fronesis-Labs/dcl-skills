---
name: dcl-prompt-firewall
description: >
  Use this skill to run a real, paid input-layer screen for prompt injection,
  jailbreak, role-switch, and instruction-override attempts via the live DCL
  Trust Oracle MCP server — before untrusted input ever reaches the model.
  Every paid call is metered and settled on-chain via the x402 protocol
  (USDC on Base) and produces a tamper-evident audit record. Use whenever an
  agent receives user-supplied or external input (user messages, tool
  results, web content, retrieved documents) and you need a pre-execution
  gate, or want a free instruction-only checklist for a quick manual review.
  Part of the Leibniz Layer Security Suite alongside DCL Policy Enforcer and
  DCL Sentinel Trace.
---

# DCL Prompt Firewall — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** Leibniz Layer Security Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Screens incoming, untrusted input — user messages, tool results, retrieved
documents, web content — for injection, jailbreak, and instruction-override
patterns *before* it reaches the model. Calls the DCL Trust Oracle and
returns a verdict (`COMMIT` / `NO_COMMIT`), a confidence score, and a
cryptographic audit record (`tx_hash`) written to a tamper-evident,
hash-chained log that stores only a hash of the input — never the raw text.

## When to trigger

- An agent receives user-supplied or external input before passing it to an LLM
- Pipeline is exposed to jailbreak, role-switch, or instruction-override attempts
- Building a multi-agent system where one agent's output becomes another's input
- Need a pre-execution audit trail alongside DCL Policy Enforcer's post-output checks
- Want a free, no-network manual review instead → use the checklist

## This skill calls a live, paid service (v3.0.0+)

Core screen runs on Fronesis Labs' **DCL Trust Oracle** MCP server, metered
and settled on-chain via **x402 in USDC on Base**. No subscription, no
account — pay per call.

Full tool list, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist: `references/free-checklist.md`.

## Where this fits in the pipeline

```
Untrusted input
        │
        ▼
DCL Prompt Firewall        ← this skill (live paid check, or free checklist)
        │ COMMIT
        ▼
      LLM
        │
        ▼
DCL Policy Enforcer        ← compliance check on output
        │ COMMIT
        ▼
DCL Sentinel Trace         ← PII redaction
        │ COMMIT
        ▼
DCL Secret Leak Detector   ← credential scan
        │ COMMIT
        ▼
DCL Semantic Drift Guard   ← hallucination check
        │ IN_COMMIT
        ▼
Safe to deliver
```

## Further reading

- `references/mcp-tools.md` — tool list, prices, connection config, call examples, output shape
- `references/free-checklist.md` — manual no-network review checklist (P1–P8)

## Privacy & data policy

Operated by Fronesis Labs. For the live tool: only a hash of the screened
text (`input_hash`) and verdict metadata are written to the audit chain —
raw input never stored. For the free checklist: everything runs inside the
agent's own context.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-policy-enforcer` — post-output compliance and content-quality check
- `dcl-sentinel-trace` — PII redaction
- `dcl-secret-leak-detector` — credential and API key scan
- `dcl-semantic-drift-guard` — hallucination and grounding check
- `dcl-skill-auditor` — pre-install scanner for ClawHub skills
