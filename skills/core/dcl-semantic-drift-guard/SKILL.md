---
name: dcl-semantic-drift-guard
description: >
  Use this skill to detect semantic hallucinations and context drift in LLM
  outputs. Triggers when an agent or pipeline needs to verify that a
  generated response is faithfully grounded in a source document that was
  already provided inline — and has not fabricated, contradicted, or
  materially distorted any claims. Default mode (source_document provided
  directly) runs entirely inside the agent's own context with no network
  calls. Two clearly-labeled optional modes exist that do transmit data
  externally: kb_query (queries a remote RAG endpoint you configure) and an
  optional paid heuristic pre-check via Fronesis Labs' live DCL Trust Oracle
  MCP server. Do not use either optional mode with confidential, regulated,
  or sensitive source material without explicit confirmation from the user.
  Returns a tamper-evident DCL audit record with verdict IN_COMMIT or
  HALLUCINATION_DRIFT. Part of the DCL Skills verification suite alongside
  DCL Policy Enforcer and DCL Sentinel Trace.
---

# DCL Semantic Drift Guard — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 1.2.0 · **Part of:** DCL Skills Verification Suite

## Data flow — read this before using

Three distinct modes, different network behavior:

| Mode | Network calls? | What leaves the agent |
|---|---|---|
| `source_mode: "context"` (default) | None | Nothing. Everything runs inside the agent's own context. |
| `source_mode: "kb_query"` | Yes | Your `kb_query` string is sent to the `kb_endpoint` you configure. |
| Optional `dcl_evaluate_quality` pre-check | Yes | The `llm_output` text is sent to Fronesis Labs' MCP server over the network, and a hash of it is written to an on-chain audit trail. |

Do not use `kb_query` mode or the optional pre-check with confidential,
regulated, or sensitive source material unless the user has explicitly
confirmed that's acceptable. When in doubt, use `source_mode: "context"`
with the document pasted directly — fully local.

## What this skill does

Compares an LLM-generated response against a trusted source of truth and
detects:

- **Hallucinated facts** — claims not present in the source
- **Logical contradictions** — statements that directly conflict with the source
- **Omission drift** — critical information from the source silently dropped
- **Fabricated specifics** — invented numbers, dates, names, clauses, identifiers

Two source modes: `context` (default, fully local, inline document) and
`kb_query` (network call to a RAG endpoint you configure — see data-flow
warning above).

Every verification produces a cryptographic audit record computed locally —
not submitted anywhere by default.

## Verdicts

| Verdict | Meaning |
|---|---|
| `IN_COMMIT` | Response is faithfully grounded in the source. Safe to proceed. |
| `HALLUCINATION_DRIFT` | Response contains fabricated, contradicted, or unsupported claims. Do not commit. Review `drift_items`. |

## Further reading

- `references/schema-and-workflow.md` — input/output schema, strictness levels, the full step-by-step verification workflow, worked examples, use-case table
- `references/optional-precheck.md` — optional paid heuristic pre-check tool, integration patterns

## Privacy & data policy

Operated by Fronesis Labs. Data handling depends on mode:

- `source_mode: "context"` (default): fully local, nothing written to disk, no logs retained.
- `source_mode: "kb_query"`: your query and retrieved chunks are handled by whatever `kb_endpoint` you configure — that endpoint's own data policy applies, not this skill's.
- Optional live pre-check: only `input_hash` and verdict metadata written to the on-chain audit trail; raw text not stored server-side.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-policy-enforcer` — policy and jailbreak detection (run before Drift Guard)
- `dcl-prompt-firewall` — input-layer injection and jailbreak detection
- `dcl-sentinel-trace` — PII redaction (run before source reaches LLM)
- `dcl-skill-auditor` — pre-install scanner for ClawHub skills
