---
name: dcl-secret-leak-detector
description: >
  Scan AI agent outputs, tool results, and pipeline data for exposed secrets
  and credentials — API keys, tokens, private keys, database URLs, .env
  values — before they reach users, logs, or downstream systems. Runs as a
  free instruction-only checklist, or as a real, paid regex scan via the
  live DCL Trust Oracle MCP server (Leibniz Layer protocol) with an
  on-chain audit proof settled via x402 (USDC on Base). The specialist
  companion to DCL Sentinel Trace — for secrets, not just PII. Part of the
  DCL Skills security suite by Fronesis Labs.
---

# DCL Secret Leak Detector — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 2.0.0 · **Part of:** DCL Skills Security Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Scans AI agent outputs, tool results, and pipeline data for exposed secrets
and credentials before they reach users, logs, or downstream systems.

Two modes, same 8 categories (S1–S8):

1. **Free, instruction-only** — the agent works through the checklist
   itself, entirely inside its own context. No network call, no charge.
2. **Paid, live** — same categories, run as real regex against the live
   DCL Trust Oracle MCP server, settled on-chain via x402, returning a
   cryptographic `tx_hash` seal.

The two modes implement the same categories and should agree. Use free mode
for manual/offline review; use live mode for an independently verifiable,
on-chain-anchored proof of the scan.

### What gets detected

| Category | Pattern class |
|---|---|
| `api_key` | Provider-prefixed keys: OpenAI, Anthropic, Stripe, GitHub, Slack, SendGrid, Twilio patterns |
| `cloud_credential` | AWS access key IDs, AWS secret access keys, GCP service account fragments |
| `token` | JWTs, Bearer tokens |
| `private_key_pem` | PEM header/footer blocks for any private key type |
| `database_url` | Connection strings with embedded credentials: `proto://user:pass@host` |
| `connection_string` | ADO.NET / ODBC style strings with `User ID=`/`Password=` fields |
| `env_assignment` | `.env`-style lines where the variable name matches known secret patterns |
| `webhook_secret` | Signed secrets for platforms like Stripe |
| `internal_endpoint` | URLs containing API keys or tokens as query parameters |

## When to trigger

- High-risk agent patterns: coding agents (shell scripts, Dockerfiles, CI
  configs, Terraform), DevOps/infra agents (deployment configs, env files,
  k8s secrets), RAG over internal docs/wikis/runbooks, tool-calling agents
  that may reproduce a key in reasoning or final response

## Live tool & free checklist

Full tool details, prices, connection config, call examples:
`references/mcp-tools.md`.

Free, no-network manual checklist (S1–S8): `references/free-checklist.md`.

## vs DCL Sentinel Trace

Complementary, not competing — run both.

| | DCL Sentinel Trace | DCL Secret Leak Detector |
|---|---|---|
| Focus | Personal identity data | Technical credentials |
| Catches | Emails, phones, national IDs, IBANs, card PANs | API keys, tokens, private keys, DB URLs |
| Primary risk | Privacy breach | Security breach / credential compromise |
| Live tool | `dcl_evaluate_pii` ($0.02) | `dcl_evaluate_secrets` ($0.02) |

A response can be PII-clean and still contain a live credential.

## Where this fits in the pipeline

```
Untrusted input
        │
        ▼
DCL Prompt Firewall          ← blocks malicious input
        │ COMMIT
        ▼
      LLM call
        │
        ▼
DCL Policy Enforcer          ← policy & jailbreak check
        │ COMMIT
        ▼
DCL Sentinel Trace           ← PII redaction
        │ COMMIT
        ▼
DCL Secret Leak Detector     ← this skill — credential & secret scan
        │ COMMIT
        ▼
DCL Semantic Drift Guard     ← hallucination & grounding check
        │ IN_COMMIT
        ▼
Safe to deliver
```

## Privacy & data policy

Operated by Fronesis Labs. Free checklist is 100% instruction-only. For the
live tool: only `input_hash` and finding metadata are written to the
on-chain audit trail; raw text and secret values never stored server-side.
Only redacted samples ever appear in output.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-sentinel-trace` — PII redaction and identity exposure detection
- `dcl-prompt-firewall` — input-layer injection and jailbreak detection
- `dcl-policy-enforcer` — policy and jailbreak detection for AI outputs
- `dcl-semantic-drift-guard` — hallucination and grounding check
