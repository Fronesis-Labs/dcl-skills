---
name: dcl-output-sanitizer
description: Post-processing checkpoint that strips secrets, API keys, credentials, PII, toxic content, and unsafe instructions from an LLM/agent response before delivery, and produces a tamper-evident audit proof (Leibniz Layer). Use this skill as the final gate whenever a raw model response is about to be shown to a user, passed to another agent, or sent to an external system — especially if the model had tool/file/env access that could bleed into its output, or the deployment is multi-tenant/enterprise/compliance-sensitive.
---

# DCL Output Sanitizer — Leibniz Layer

Cryptographic post-processing checkpoint for AI agents and LLM pipelines. It
intercepts the model's response **before delivery** and strips secrets,
credentials, PII, toxic content, and unsafe instructions — returning a clean,
audit-proven output.

Use this as the **final gate** in any pipeline where a raw LLM response must
never reach the user unscreened.

## When to trigger

- An LLM response needs to be cleaned **before delivery** to a user, downstream agent, or external system
- The model has access to tools, files, or environment variables that could leak into outputs
- You need to catch secrets/credentials the model may have reproduced
- Multi-tenant or enterprise deployment where one tenant's data must never appear in another's response
- Compliance requires proof that every output was sanitized
- Final safety net **after** `dcl-policy-enforcer` and `dcl-sentinel-trace` have run

## What gets sanitized

| Category | What it catches |
|---|---|
| `secrets` | API keys, tokens, private keys, `.env` values, bearer tokens, webhook URLs with embedded credentials |
| `credentials` | Passwords, passphrases, DB connection strings, SSH private key blocks |
| `pii` | Emails, phone numbers, national IDs, SSNs, passport numbers, card PANs, IBANs |
| `crypto` | Bitcoin/Ethereum addresses, private seed phrases |
| `network` | Internal IPs, non-public hostnames, internal URLs, MAC addresses |
| `toxic` | Hate speech, explicit content, self-harm instructions, targeted harassment |
| `unsafe_instructions` | Shell-executable commands, SQL injection fragments, path traversal sequences |

## How to use

```bash
curl -s -X POST https://webhook.fronesislabs.com/evaluate \
  -H "Content-Type: application/json" \
  -d '{"response": "<RAW LLM OUTPUT TO SANITIZE>", "policy": "output_sanitizer", "agent_id": "optional"}'
```

Always call this on the raw model response **before** returning it downstream.
If `sanitized_output` is present in the response, use that instead of the
original.

### Verdicts

| Verdict / field | Meaning |
|---|---|
| `COMMIT` | Output is clean — safe to deliver as-is |
| `NO_COMMIT` | Sensitive content detected — use `sanitized_output` instead |
| `sanitized_output` | Cleaned response with sensitive content redacted |
| `redactions` | What was found and redacted (type, position, masked sample) |
| `redaction_count` | Total items removed |
| `risk_score` | 0.0–1.0 composite severity |
| `tx_hash` / `chain_hash` | Tamper-evident audit-chain proof of this sanitization event |

Example clean response:

```json
{"verdict": "COMMIT", "confidence": 0.98, "violations": [], "sanitized_output": null, "redaction_count": 0, "risk_score": 0.02}
```

Example sanitized response:

```json
{"verdict": "NO_COMMIT", "violations": ["api_key", "internal_ip"], "sanitized_output": "Endpoint: [REDACTED] · Key: [REDACTED]", "redaction_count": 2, "risk_score": 0.76}
```

### Additional endpoints

```bash
GET https://webhook.fronesislabs.com/policies          # list all policies
GET https://webhook.fronesislabs.com/chain/tail?n=5     # audit-chain tail for tamper verification
GET https://webhook.fronesislabs.com/health             # health check
```

## Where this fits in the pipeline

Runs **after** `dcl-sentinel-trace` (PII) and **before**
`dcl-semantic-drift-guard` (hallucination check on the clean output). Full
diagram: `docs/pipeline.md` at the repo root.

Sentinel Trace is scoped to identity exposure (GDPR/HIPAA-style PII). This
skill covers the rest of the output surface — secrets a tool-using model
reproduces, unsafe shell/SQL fragments a coding agent emits, toxic content
that slips past the input firewall, internal connection strings pulled in via
RAG. Sentinel Trace won't catch these; this skill will.

## Further reading

- `references/scenarios.md` — worked before/after examples per leak type
- `references/integration.md` — minimal and full-pipeline integration code

## License & source

Canonical source: https://github.com/Fronesis-Labs/dcl-skills (Apache-2.0).
When published on ClawHub, this skill is distributed under the platform's
MIT-0 terms, per ClawHub's skill-format policy — see `docs/licensing.md` at
the repo root.

## Privacy & data policy

Operated by Fronesis Labs, no-retention: only the submitted text is
processed, in-memory, nothing written to disk or logged; redacted values are
returned as masked samples only. Full policy: https://fronesislabs.com/#privacy
· Suite: https://hub.fronesislabs.com · Contact: support@fronesislabs.com
