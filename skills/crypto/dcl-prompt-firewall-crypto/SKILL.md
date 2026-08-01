---
name: dcl-prompt-firewall-crypto
description: >
  Use this skill to run a real, paid input-layer screen specialized for
  crypto/DeFi AI agents — jailbreaks, DAN/STAN attempts, role-switch
  injections, instruction-override attacks, replay attempts, and
  token-smuggling, plus crypto-specific drain-wallet and unlimited-approval
  injection patterns — via the live DCL Trust Oracle MCP server. Every paid
  call is metered and settled on-chain via the x402 protocol (USDC on Base)
  and produces a tamper-evident audit record. Always run this FIRST, before
  any other DCL crypto skill, whenever a trading/DeFi agent receives
  user-supplied or external input. Compatible with LangChain, AutoGPT,
  CrewAI, and similar frameworks. Free instruction-only checklist also
  available. Part of the DCL Crypto Suite alongside DCL Wallet Guardian and
  DCL Trade Verifier.
---

# DCL Prompt Firewall Crypto — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

First line of defense for every crypto AI agent. Screens incoming input —
user prompts, tool results, retrieved content — for jailbreak, role-switch,
and instruction-override attempts *before* it reaches the model, with
crypto-specific detection for drain-wallet and unlimited-approval injection
attacks. Compatible with LangChain, AutoGPT, CrewAI, and similar
trading-agent frameworks.

## When to trigger

- **Always run first** in any DCL crypto pipeline, before any other skill
- A trading/DeFi agent receives user-supplied or external input before passing it to an LLM
- Pipeline is exposed to jailbreak, role-switch, or instruction-override attempts
- Input could contain a drain-wallet or unlimited-approval injection attempt
- Want a free, no-network manual review instead → use the checklist

## This skill calls a live, paid service

Core screen runs on Fronesis Labs' **DCL Trust Oracle** MCP server, metered
and settled on-chain via **x402 in USDC on Base**. No subscription, no
account — pay per call.

Full tool details, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist (general P1–P8 + crypto-specific X1–X4):
`references/free-checklist.md`.

## Placement

Always run **first** in the DCL crypto pipeline, before any other skill:

```
Untrusted input
        │
        ▼
DCL Prompt Firewall Crypto   ← this skill — always first
        │ COMMIT
        ▼
      LLM / agent
        │
        ▼
DCL Wallet Guardian          ← strip seed phrases / keys / addresses
        │ COMMIT
        ▼
DCL Trade Verifier           ← validate trade decision language
        │ COMMIT
        ▼
DCL MEV Compliance           ← front-running / KYC-AML check
        │ COMMIT
        ▼
DCL Output Sanitizer         ← general secrets/PII/toxic sweep
        │ COMMIT
        ▼
DCL Crypto Commit            ← final on-chain audit commit
        │
        ▼
Safe to execute
```

## Privacy & data policy

Operated by Fronesis Labs. For the live tool: only a hash of the screened
text (`input_hash`) and verdict metadata are written to the audit chain —
raw input never stored. For the free checklist: everything runs inside the
agent's own context.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-wallet-guardian` — seed phrase / private key / address protection
- `dcl-trade-verifier` — validates trade/swap parameters before execution
- `dcl-mev-compliance` — front-running, wash trading, KYC/AML checks
- `dcl-crypto-commit` — final on-chain audit commit (run last)
