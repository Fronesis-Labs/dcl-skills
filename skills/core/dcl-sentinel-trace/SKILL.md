---
name: dcl-sentinel-trace
description: >
  Detect and redact personally identifiable information in AI outputs before
  they reach users or downstream systems — emails, phones, national IDs,
  bank cards, IBANs, crypto addresses, IPs, passports. Runs as a free
  instruction-only checklist, or as a real, paid regex scan (with a Luhn
  checksum on card numbers) via the live DCL Trust Oracle MCP server
  (Leibniz Layer protocol), settled on-chain via x402 (USDC on Base). Part
  of the DCL Skills security suite by Fronesis Labs.
---

# DCL Sentinel Trace — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Skills Security Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Detects and redacts personally identifiable information in AI outputs
before they reach users or downstream systems.

Two modes, same 8 categories (T1–T8):

1. **Free, instruction-only** — the agent works through the checklist
   itself, entirely inside its own context. No network call, no charge.
2. **Paid, live** — same categories, run as real regex (plus a Luhn
   checksum on card numbers to cut false positives) against the live DCL
   Trust Oracle MCP server, settled on-chain via x402, returning a
   cryptographic `tx_hash` seal.

Close one-to-one match: the live tool implements the same categories
documented here. Use free mode for manual/offline review; use live mode for
an independently verifiable, on-chain-anchored proof of the scan.

### What gets detected

| Category | Examples |
|---|---|
| `email` | Any email address pattern |
| `phone` | International format numbers (with country code) |
| `national_id` | US-style SSN pattern (`###-##-####`) |
| `bank_card` | Card PANs, verified with a Luhn checksum to reduce false positives |
| `iban` | International bank account numbers |
| `crypto_address` | Bitcoin and Ethereum wallet address formats |
| `ip_address` | IPv4 and IPv6 addresses |
| `passport` | Passport/document numbers appearing in explicit passport context |

## When to trigger

- AI output may contain personal data from user input, documents, or retrieved content
- A coding or data agent processes datasets that may contain real PII
- Need a privacy checkpoint before logging or storing AI outputs

## Live tool & free checklist

Full tool details, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist (T1–T8): `references/free-checklist.md`.

## vs DCL Secret Leak Detector

Complementary, not competing — run both.

| | DCL Sentinel Trace | DCL Secret Leak Detector |
|---|---|---|
| Focus | Personal identity data | Technical credentials |
| Catches | Emails, phones, national IDs, IBANs, card PANs | API keys, tokens, private keys, DB URLs |
| Primary risk | Privacy breach | Security breach / credential compromise |
| Live tool | `dcl_evaluate_pii` ($0.02) | `dcl_evaluate_secrets` ($0.02) |

A response can be free of credentials and still expose personal data.

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
DCL Policy Enforcer        ← policy check on output
        │ COMMIT
        ▼
DCL Sentinel Trace         ← this skill — PII redaction
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

## Privacy & data policy

Operated by Fronesis Labs. Free checklist is 100% instruction-only. For the
live tool: only `input_hash` and finding metadata are written to the
on-chain audit trail; raw text and detected personal data never stored
server-side. Only redacted samples ever appear in output.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-secret-leak-detector` — credential and API key scan
- `dcl-prompt-firewall` — input-layer injection and jailbreak detection
- `dcl-policy-enforcer` — policy and jailbreak detection for AI outputs
- `dcl-semantic-drift-guard` — hallucination and grounding check
