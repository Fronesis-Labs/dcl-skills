---
name: dcl-wallet-guardian
description: Masks seed phrases, private keys, mnemonics, and wallet addresses before they reach any LLM context window, and re-scans agent output before delivery. Detects accidental exposure of sensitive self-custody data in prompts, agent outputs, or RAG-retrieved content. Use whenever an agent handles wallet setup, key management, transaction signing, or any DeFi workflow where a user or retrieved document might contain a seed phrase, private key, or wallet address. GDPR and HIPAA compatible. Part of the DCL Crypto Suite alongside DCL Trade Verifier and DCL Output Sanitizer.
---

# DCL Wallet Guardian — Leibniz Layer

Cryptographic pre/post-processing checkpoint specialized for wallet and
self-custody data. It intercepts text — inbound user input, retrieved
documents, or an agent's own response — and strips seed phrases, private
keys, wallet addresses, and wallet-context credentials before they persist
anywhere.

## When to trigger

- An agent handles wallet setup, key import, or recovery-phrase workflows
- A signing or transaction-broadcast agent may echo a private key in its reasoning or output
- A RAG pipeline retrieves internal docs (runbooks, configs) that may contain treasury addresses or credentials
- A tool-calling agent authenticates to a wallet/custody API and could reproduce the token
- You want a pre-context gate (before the LLM sees sensitive input) as well as a pre-delivery gate (before the response goes out)

## What gets detected

| Category | What it catches |
|---|---|
| `seed_phrase` | BIP-39 mnemonics, 12 or 24 words |
| `private_key` | Hex-format (`0x...`) and WIF-format private keys |
| `wallet_address` | ETH addresses, BTC addresses (`bc1...`, `1...`, `3...`) |
| `wallet_api_credential` | API keys and bearer tokens appearing in a wallet/custody context |

## How to use

```bash
curl -s -X POST https://webhook.fronesislabs.com/evaluate \
  -H "Content-Type: application/json" \
  -d '{"response": "<TEXT TO SCAN>", "policy": "wallet_guardian", "agent_id": "optional"}'
```

Call this on any text before it enters the model's context (inbound) and
again on the raw model response before delivery (outbound). If
`sanitized_output` is present in the response, use that instead of the
original.

### Verdicts

| Verdict / field | Meaning |
|---|---|
| `COMMIT` | Clean — safe to use as-is |
| `NO_COMMIT` | Sensitive wallet data detected — use `sanitized_output` instead |
| `sanitized_output` | Cleaned text with sensitive data redacted |
| `redactions` | What was found and redacted (type, position, masked sample) |
| `redaction_count` | Total items removed |
| `risk_score` | 0.0–1.0 composite severity |
| `tx_hash` / `chain_hash` | Tamper-evident audit-chain proof of this check |

Example clean response:

```json
{"verdict": "COMMIT", "confidence": 0.98, "violations": [], "sanitized_output": null, "redaction_count": 0, "risk_score": 0.02}
```

Example sanitized response:

```json
{"verdict": "NO_COMMIT", "violations": ["seed_phrase"], "sanitized_output": "My seed phrase is: [REDACTED — SEED_PHRASE], and the tx still fails.", "redaction_count": 1, "risk_score": 0.95}
```

### Additional endpoints

```bash
GET https://webhook.fronesislabs.com/policies          # list all policies
GET https://webhook.fronesislabs.com/chain/tail?n=5     # audit-chain tail for tamper verification
GET https://webhook.fronesislabs.com/health             # health check
```

## Where this fits

Runs at **both ends** of a wallet-adjacent interaction — before sensitive
input reaches the model, and again before the response is delivered. See
`references/integration.md` for the full position diagram alongside
`dcl-output-sanitizer`.

## Further reading

- `references/scenarios.md` — worked before/after examples per exposure type
- `references/integration.md` — minimal integration snippet and pipeline position

## Privacy & data policy

Operated by Fronesis Labs under a strict no-retention data policy. Only the
text submitted for scanning is processed, in-memory, nothing written to
disk or logged. Detected secrets are returned as masked samples only — raw
values are never stored or logged.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-output-sanitizer` — general secrets/PII/toxic-content sweep (run alongside for non-wallet-specific leaks)
- `dcl-trade-verifier` — validates trade/swap parameters before execution
- `dcl-crypto-commit` — final compliance checkpoint before irreversible on-chain action
