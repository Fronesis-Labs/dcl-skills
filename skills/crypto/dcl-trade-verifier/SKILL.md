---
name: dcl-trade-verifier
description: >
  Use this skill to run a real, paid deterministic audit of an AI-generated
  trade decision via the live DCL Trust Oracle MCP server. Blocks
  guaranteed-return claims, zero-risk framing, and unqualified buy/sell
  directives; requires a risk disclosure to be present. Generates an
  immutable trade receipt (tx_hash + chain_hash) cryptographically linked to
  the DCL audit chain, settled on-chain via x402 (USDC on Base). For
  algo-trading, copy-trading, and institutional signal providers. Free
  instruction-only checklist also available. Part of the DCL Crypto Suite
  alongside DCL MEV Compliance and DCL Crypto Commit.
---

# DCL Trade Verifier — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Deterministic audit of every AI-generated trade decision before it's acted
on or shown to a user. Blocks guaranteed-profit claims and unqualified
advice, and generates an immutable trade receipt cryptographically linked
to the DCL audit chain. Built for algo-trading, copy-trading, and
institutional signal providers.

## When to trigger

- An agent generates a trade/swap recommendation or executes one autonomously
- A copy-trading or signal-provider agent's output will be shown to end users
- Need an immutable audit receipt for a trading decision
- Want a free, no-network manual review instead → use the checklist

## This skill calls a live, paid service

Core check runs on Fronesis Labs' **DCL Trust Oracle** MCP server, metered
and settled on-chain via **x402 in USDC on Base**. No subscription, no
account — pay per call.

Full tool details, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist: `references/free-checklist.md`. Note:
the free checklist can flag risky language, but cannot produce a real
on-chain trade receipt — that requires the live tool.

## Required disclosure

Trade output must contain the word **"risk"** somewhere to pass — a bare
recommendation with no risk framing at all is treated as a violation, not
just weak phrasing.

## Where this fits

```
DCL Prompt Firewall Crypto   ← input screened first
        │ COMMIT
        ▼
      LLM / agent generates trade decision
        │
        ▼
DCL Trade Verifier           ← this skill
        │ COMMIT
        ▼
DCL MEV Compliance           ← front-running / KYC-AML check
        │ COMMIT
        ▼
DCL Crypto Commit            ← final on-chain audit commit
        │
        ▼
Safe to execute / deliver
```

## Privacy & data policy

Operated by Fronesis Labs. For the live tool: only `input_hash` and finding
metadata are written to the audit chain — the raw trade text is never
stored server-side. For the free checklist: everything runs inside the
agent's own context.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-prompt-firewall-crypto` — input-layer injection and jailbreak detection (run first)
- `dcl-mev-compliance` — front-running, wash trading, KYC/AML checks
- `dcl-semantic-drift-crypto` — hallucination guard for market signals and price feeds
- `dcl-crypto-commit` — final on-chain audit commit (run last)
