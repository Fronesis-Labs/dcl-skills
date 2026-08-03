---
name: dcl-policy-enforcer
description: >
  Use this skill to run a pre-action compliance audit of an AI agent or LLM
  response — either a real, paid check via the live DCL Trust Oracle MCP
  server (jailbreak / instruction-override detection, baseline safety,
  content-quality drift, pattern-based regulatory checklists for
  transparency, data handling, financial and medical disclosure), or a free
  instruction-only manual checklist with no network call. Use whenever you
  need to gate a risky agent action before it executes, screen an LLM output
  for jailbreak attempts, or want a durable audit trail for a decision. Part
  of the Leibniz Layer Security Suite alongside dcl-prompt-firewall and
  dcl-sentinel-trace.
---

# DCL Policy Enforcer — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.1.0 · **Part of:** Leibniz Layer Security Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Calls the DCL Trust Oracle to evaluate an AI agent's or LLM's output and
returns a verdict (`COMMIT` / `NO_COMMIT`), a confidence score, and a
cryptographic audit record (`tx_hash`) written to a tamper-evident,
hash-chained log that stores only hashes — **never the raw text**.

A **free, instruction-only checklist** is also available for a manual,
no-payment, no-network-call review — see `references/free-checklist.md`.

## When to trigger

- Gate a risky agent action before it executes
- Screen an LLM output for jailbreak / instruction-override attempts
- Run a baseline safety pass, or a content-quality / drift check
- Get a durable, on-chain-anchored audit trail for a decision
- User wants a quick manual review with no network call → use the free checklist instead

## This skill calls a live, paid service (v3.0.0+)

The core evaluation runs on Fronesis Labs' **DCL Trust Oracle** MCP server —
a real backend, not a local simulation. Each paid tool call is metered and
settled on-chain via the **x402 protocol in USDC on the Base network**. No
subscription, no account — the calling agent (or its wallet-enabled MCP
client) pays per call.

Full tool list, prices, connection config, and call examples:
`references/mcp-tools.md`.

If you'd rather not make a paid call, use the free checklist instead:
`references/free-checklist.md`.

## Where this fits in the pipeline

```
Untrusted input
        │
        ▼
DCL Prompt Firewall        ← blocks malicious input
        │ COMMIT
        ▼
      LLM
        │
        ▼
DCL Policy Enforcer        ← this skill (live paid check, or free checklist)
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

See `docs/pipeline.md` at the repo root for the full diagram this fits into.

## Further reading

- `references/mcp-tools.md` — full tool list, prices, connection config, call examples, output shape
- `references/free-checklist.md` — manual no-network review checklist

## License & source

Canonical source: https://github.com/Fronesis-Labs/dcl-skills (Apache-2.0).
When published on ClawHub, this skill is distributed under the platform's
MIT-0 terms — see `docs/licensing.md` at the repo root, which also flags an
open question about the price table in `references/mcp-tools.md` versus
ClawHub's no-pricing-metadata policy.

## Privacy & data policy

Operated by Fronesis Labs. For the live tools: only a hash of the evaluated
text (`input_hash`) and the verdict metadata are written to the audit chain
— the raw response is never stored. For the free checklist: everything runs
inside the agent's own context; nothing is transmitted anywhere.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-prompt-firewall` — input-layer injection and jailbreak detection
- `dcl-sentinel-trace` — PII redaction
- `dcl-secret-leak-detector` — credential and API key scan
- `dcl-semantic-drift-guard` — hallucination and grounding check
