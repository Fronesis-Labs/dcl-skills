---
name: dcl-provenance-tracker-crypto
description: >
  Supply chain verification for on-chain AI skills and smart contract
  integrations. Compares a trusted baseline against a candidate update and
  detects eval/exec obfuscation, selfdestruct, delegatecall, tx.origin
  authorization, credential exfiltration, undeclared network calls, and new
  unlimited-approval requests. 100% instruction-only for the diff itself —
  no external calls, no code leaves the agent. Optionally cross-check a past
  scan's on-chain integrity via the live DCL Trust Oracle MCP server (x402,
  USDC on Base). Use before every skill or contract-integration update, and
  in CI/CD pipelines. Part of the DCL Crypto Suite, the on-chain counterpart
  to DCL Provenance Tracker.
---

# DCL Provenance Tracker Crypto — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 1.1.0 · **Part of:** DCL Crypto Suite

## What this skill does

Supply chain verification for on-chain AI skills and smart contract
integrations. Compares two versions — a trusted baseline and a candidate
update — and detects behavioral drift, permission creep, and supply chain
attack patterns specific to on-chain code, on top of the general patterns
covered by `dcl-provenance-tracker`.

**The version-diff logic is 100% instruction-only.** No external network
calls are made for the comparison itself. The user provides both versions
directly; the agent analyzes them locally.

## When to trigger

- Immediately after any update to a skill or smart-contract integration
- Before deploying an agent that interacts with a new or updated contract
- In CI/CD pipelines before an on-chain agent goes live
- When a contract-integration's behavior seems to have changed unexpectedly

## How to run a provenance check

Same workflow as `dcl-provenance-tracker`: user pastes both baseline and
candidate content directly; no fetching from URLs.

1. Confirm both versions are in context
2. Compute `baseline_hash` / `candidate_hash` (SHA-256) — if identical, verdict is `PASS`
3. Generate a structured diff
4. Run the drift checklist — general D1–D5 from `dcl-provenance-tracker`, plus on-chain-specific D1c–D4c in `references/drift-checklist.md`
5. Apply verdict logic: any critical finding → `BLOCK`; 2+ major → `BLOCK`; 1 major or any minor → `WARN`; none → `PASS`
6. Compute `dcl_fingerprint = "DCL-PTC-" + date + "-" + candidate_hash[:8] + "-" + analysis_hash[:8]`

Output schema matches `dcl-provenance-tracker`'s (`verdict`, `risk_score`,
`findings[]`, `dcl_fingerprint`, etc.) with `pattern_id` values drawn from
the D1c–D4c categories where applicable.

## Further reading

- `references/drift-checklist.md` — on-chain-specific categories D1c–D4c (dangerous contract primitives, obfuscated payloads, undeclared network/credential access, approval changes)
- `references/onchain-crosscheck.md` — optional on-chain record lookup, CI/CD integration pattern

## Privacy & data policy

Operated by Fronesis Labs. The version-diff logic is 100% instruction-only
— both versions are analyzed entirely within the agent's context window.
The optional cross-check only retrieves metadata about a record you already
created.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-provenance-tracker` — general-purpose version of this skill (non-on-chain-specific patterns)
- `dcl-mev-compliance` — front-running, wash trading, KYC/AML checks
- `dcl-wallet-guardian` — seed phrase / private key / address protection
