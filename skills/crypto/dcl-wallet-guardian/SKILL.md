---
name: dcl-wallet-guardian
description: >
  Use this skill to run a real, paid scan for seed phrases, private keys,
  wallet addresses, and wallet-context API credentials via the live DCL
  Trust Oracle MCP server — before sensitive self-custody data reaches an
  LLM context window or leaves it in a response. Every paid call is metered
  and settled on-chain via the x402 protocol (USDC on Base) and produces a
  tamper-evident audit record. Use whenever an agent handles wallet setup,
  key management, transaction signing, or any DeFi workflow where a user or
  retrieved document might contain a seed phrase, private key, or wallet
  address — or want a free instruction-only checklist for a quick manual
  review. GDPR and HIPAA compatible. Part of the DCL Crypto Suite alongside
  DCL Trade Verifier and DCL Output Sanitizer.
---

# DCL Wallet Guardian — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Scans text — inbound user input, retrieved documents, or an agent's own
response — for seed phrases, private keys, wallet addresses, and
wallet-context credentials. Calls the DCL Trust Oracle and returns a verdict
(`COMMIT` / `NO_COMMIT`), a confidence score, and a cryptographic audit
record (`tx_hash`) written to a tamper-evident, hash-chained log that stores
only a hash of the input — never the raw text.

A **free, instruction-only checklist** is also available for a manual,
no-payment, no-network-call review — see `references/free-checklist.md`.

## When to trigger

- An agent handles wallet setup, key import, or recovery-phrase workflows
- A signing or transaction-broadcast agent may echo a private key in its reasoning or output
- A RAG pipeline retrieves internal docs that may contain treasury addresses or credentials
- A tool-calling agent authenticates to a wallet/custody API and could reproduce the token
- Want a pre-context gate (before the LLM sees sensitive input) as well as a pre-delivery gate (before the response goes out)
- Want a free, no-network manual review instead → use the checklist

## What gets detected

| Category | What it catches |
|---|---|
| `seed_phrase` | BIP-39 mnemonics, 12 or 24 words |
| `private_key` | Hex-format (`0x...`) and WIF-format private keys |
| `wallet_address` | ETH addresses, BTC addresses (`bc1...`, `1...`, `3...`) |
| `wallet_api_credential` | API keys and bearer tokens appearing in a wallet/custody context |

## This skill calls a live, paid service (v3.0.0+)

The core scan runs on Fronesis Labs' **DCL Trust Oracle** MCP server — a real
backend, not a local simulation. Each paid tool call is metered and settled
on-chain via the **x402 protocol in USDC on the Base network**. No
subscription, no account — the calling agent (or its wallet-enabled MCP
client) pays per call.

Full tool details, prices, connection config, call examples, output shape:
`references/mcp-tools.md`.

If you'd rather not make a paid call, use the free checklist instead:
`references/free-checklist.md`.

## Where this fits

Runs at **both ends** of a wallet-adjacent interaction — before sensitive
input reaches the model, and again before the response is delivered:

```
User input / retrieved doc
        │
        ▼
DCL Wallet Guardian     ← strip seed phrases / private keys / addresses
        │ COMMIT
        ▼
      LLM / agent
        │
        ▼
DCL Wallet Guardian     ← re-run on output before delivery
        │ COMMIT
        ▼
DCL Output Sanitizer    ← general secrets/PII/toxic sweep
        │ COMMIT
        ▼
Safe to deliver
```

## Further reading

- `references/mcp-tools.md` — tool details, prices, connection config, call examples, output shape
- `references/free-checklist.md` — manual no-network review checklist (W1–W4)
- `references/scenarios.md` — illustrative exposure patterns this skill catches

## Privacy & data policy

Operated by Fronesis Labs. For the live tool: only a hash of the scanned
text (`input_hash`) and verdict metadata are written to the audit chain —
raw text and any real key/seed values are never stored. For the free
checklist: everything runs inside the agent's own context; nothing is
transmitted anywhere.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-output-sanitizer` — general secrets/PII/toxic-content sweep (run alongside for non-wallet-specific leaks)
- `dcl-trade-verifier` — validates trade/swap parameters before execution
- `dcl-crypto-commit` — final compliance checkpoint before irreversible on-chain action
