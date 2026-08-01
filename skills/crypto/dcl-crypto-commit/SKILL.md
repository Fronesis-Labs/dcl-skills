---
name: dcl-crypto-commit
description: >
  Commits every AI trading/agent decision to the Leibniz Layer
  tamper-evident, append-only audit chain via the live DCL Trust Oracle MCP
  server. Returns tx_hash + chain_hash as a Merkle-proof receipt,
  cryptographically linked to the previous commit. Settled on-chain via
  x402 (USDC on Base). Always run LAST in the DCL crypto pipeline — after
  every other check (prompt firewall, wallet guardian, trade verifier, MEV
  compliance, output sanitizer) has passed. For algo-trading and DeFi
  agents needing an institutional-grade audit trail. Part of the DCL Crypto
  Suite.
---

# DCL Crypto Commit — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Tamper-evident commitment layer for every trading decision. Every commit is
cryptographically linked to the previous one, forming an append-only audit
trail purpose-built for algo-trading and DeFi agents.

Unlike the other DCL crypto skills, this one has **no meaningful free
alternative** — the entire point is the on-chain, chain-linked commit. See
`references/local-fingerprint-caveat.md` if you need an offline placeholder,
but it is explicitly not equivalent.

## When to trigger

- **Always run last**, after every other check in the pipeline has passed
- A trading decision or DeFi agent action is about to be executed and needs a permanent audit record
- Building an institutional audit trail that regulators or counterparties may need to verify later

## This skill calls a live, paid service

Runs on Fronesis Labs' **DCL Trust Oracle** MCP server, metered and settled
on-chain via **x402 in USDC on Base**. No subscription, no account — pay
per call.

Full tool details, price, connection config, call example, output shape:
`references/mcp-tools.md`.

## Placement

Always run **last**, after all other checks pass:

```
DCL Prompt Firewall Crypto   ← input screened first
        │ COMMIT
        ▼
DCL Wallet Guardian          ← strip seed phrases / keys / addresses
        │ COMMIT
        ▼
DCL Trade Verifier           ← trade decision language check
        │ COMMIT
        ▼
DCL MEV Compliance           ← front-running / KYC-AML check
        │ COMMIT
        ▼
DCL Output Sanitizer         ← general secrets/PII/toxic sweep
        │ COMMIT
        ▼
DCL Crypto Commit            ← this skill — always last
        │
        ▼
Executed / delivered, with full audit chain
```

## Privacy & data policy

Operated by Fronesis Labs. Only `input_hash` and commit metadata are
written to the audit chain — the raw decision text is never stored
server-side beyond what's needed to compute the hash.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-trade-verifier` — deterministic audit of trade decisions (run before this)
- `dcl-mev-compliance` — front-running, wash trading, KYC/AML checks (run before this)
- `dcl-prompt-firewall-crypto` — input-layer injection and jailbreak detection (run first)
