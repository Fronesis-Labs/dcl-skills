---
name: dcl-mev-compliance
description: >
  Use this skill to run a real, paid MEV and regulatory compliance screen on
  AI agent outputs via the live DCL Trust Oracle MCP server — front-running
  and sandwich attack patterns, wash trading / layering / spoofing, KYC/AML
  red flags, and pump-and-dump / rug-pull language. EU AI Act Art. 9/13
  aligned, MiFID II / FCA compliant framing. For institutional DeFi desks
  and RWA platforms. Every paid call is metered and settled on-chain via the
  x402 protocol (USDC on Base) and produces a tamper-evident audit record
  for regulatory purposes. Free instruction-only checklist also available.
  Part of the DCL Crypto Suite alongside DCL Trade Verifier and DCL Crypto
  Commit.
---

# DCL MEV Compliance Shield — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

MEV protection + KYC/AML + EU AI Act compliance screen for DeFi agents.
Screens agent outputs for front-running/sandwich-attack patterns, wash
trading, layering, spoofing, KYC/AML red flags, and pump-and-dump/rug-pull
language, and returns a verdict with a regulatory-audit-grade `tx_hash`.

## When to trigger

- Institutional DeFi desk or RWA platform agent output needs a compliance screen
- An agent's output describes or proposes MEV-adjacent trading behavior
- Need an audit trail referencing EU AI Act Art. 9/13, MiFID II, or FCA obligations
- Want a free, no-network manual review instead → use the checklist

## This skill calls a live, paid service

Core screen runs on Fronesis Labs' **DCL Trust Oracle** MCP server, metered
and settled on-chain via **x402 in USDC on Base**. No subscription, no
account — pay per call.

Full tool details, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist (M1–M4): `references/free-checklist.md`.
Note: the free checklist flags language patterns only — it does not analyze
actual mempool or transaction data.

## Where this fits

```
DCL Prompt Firewall Crypto   ← input screened first
        │ COMMIT
        ▼
DCL Trade Verifier           ← trade decision language check
        │ COMMIT
        ▼
DCL MEV Compliance           ← this skill
        │ COMMIT
        ▼
DCL Output Sanitizer         ← general secrets/PII/toxic sweep
        │ COMMIT
        ▼
DCL Crypto Commit            ← final on-chain audit commit
        │
        ▼
Safe to execute / deliver
```

## Privacy & data policy

Operated by Fronesis Labs. For the live tool: only `input_hash` and finding
metadata are written to the audit chain — raw text never stored
server-side. For the free checklist: everything runs inside the agent's own
context.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-trade-verifier` — deterministic audit of trade decisions
- `dcl-prompt-firewall-crypto` — input-layer injection and jailbreak detection (run first)
- `dcl-crypto-commit` — final on-chain audit commit (run last)
