---
name: dcl-semantic-drift-crypto
description: >
  Use this skill to detect hallucinations in crypto market signals and
  price feeds — fabricated prices, invented token names, contradicted
  indicators, and overconfident/guaranteed-outcome language. Two paths: a
  real, paid pattern-based pre-check via the live DCL Trust Oracle MCP
  server (x402, USDC on Base), and a free, local, instruction-only workflow
  that grounds signal claims against a source document (price feed
  snapshot) pasted directly into context. Use strictness "strict" for
  automated trade triggers, "balanced" for analyst summaries, "lenient" for
  exploratory research. Part of the DCL Crypto Suite alongside DCL Trade
  Verifier and DCL MEV Compliance.
---

# DCL Semantic Drift Guard Crypto — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 3.0.0 · **Part of:** DCL Crypto Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Hallucination guard for market signals and price data. Two ways to run it:

1. **Live, paid pre-check** — pattern-based heuristic on the signal text
   alone (no source document needed), settled on-chain via x402. Fast,
   cheap, catches overconfident/certain-outcome language and obviously
   fabricated figures.
2. **Free, local grounding workflow** — claim-by-claim check of the signal
   output against an actual source document (price feed snapshot, on-chain
   data pull) you paste into context. No network call. More thorough when
   you have a real source to check against.

## When to trigger

- A signal-provider or trading-analysis agent's output will be shown to a user or acted on
- Output makes specific price, percentage, or directional claims
- Need to catch guaranteed-outcome language before an automated trade trigger fires
- Have a source document (price feed, on-chain data) to ground against → use the local workflow
- Just want a fast heuristic screen with no source document → use the live pre-check

## Live pre-check (paid) vs. local grounding workflow (free)

Full tool details, prices, connection config, call examples for the live
pre-check: `references/mcp-tools.md`.

Full input/output schema and step-by-step process for the local grounding
workflow: `references/grounding-workflow.md`.

## Strictness

| Level | Use for |
|---|---|
| `strict` | Automated trade triggers |
| `balanced` | Analyst summaries |
| `lenient` | Exploratory research |

## Where this fits

```
DCL Prompt Firewall Crypto   ← input screened first
        │ COMMIT
        ▼
      LLM / agent generates signal or analysis
        │
        ▼
DCL Semantic Drift Crypto    ← this skill
        │ IN_COMMIT
        ▼
DCL Trade Verifier           ← trade decision language check (if acted on)
        │ COMMIT
        ▼
Safe to deliver / execute
```

## Privacy & data policy

Operated by Fronesis Labs. Live pre-check: only `input_hash` and verdict
metadata written to the on-chain audit trail, raw text not stored
server-side. Local grounding workflow: fully local, nothing transmitted
anywhere.

Full policy: https://fronesislabs.com/#privacy · Suite:
https://hub.fronesislabs.com · Contact: support@fronesislabs.com

## Related skills

- `dcl-trade-verifier` — deterministic audit of trade decisions
- `dcl-mev-compliance` — front-running, wash trading, KYC/AML checks
- `dcl-prompt-firewall-crypto` — input-layer injection and jailbreak detection
