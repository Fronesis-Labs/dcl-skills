# Pipeline

How the core skills chain together via `dcl-pipeline` (meta-skill):

```
input / tool output
      │
      ▼
dcl-prompt-firewall     — block injection payloads
      │
      ▼
dcl-secret-leak-detector — strip leaked credentials
      │
      ▼
dcl-sentinel-trace       — redact PII
      │
      ▼
dcl-semantic-drift-guard — flag hallucination vs grounding
      │
      ▼
dcl-policy-enforcer      — final policy check (paid x402 audit or free manual)
      │
      ▼
   output
```

Crypto variants (`skills/crypto/`) slot in the same positions when the agent
is acting on-chain, with `dcl-wallet-guardian`, `dcl-trade-verifier`,
`dcl-mev-compliance`, and `dcl-crypto-commit` added before any irreversible
transaction broadcast.

## Migration status from ClawHub

| Skill | ClawHub status | Repo status |
|---|---|---|
| dcl-policy-enforcer | published, x402 paid audit live, v3.0.0 | **done** — content migrated; price table + free checklist split into references/ |
| dcl-sentinel-trace | published | **done** — content migrated; checklist + mcp-tools split into references/ |
| dcl-prompt-firewall | published | **done** — content migrated; checklist + mcp-tools split into references/ |
| dcl-secret-leak-detector | published | **done** — content migrated; checklist + mcp-tools split into references/ |
| dcl-provenance-tracker | published (new folder, not in original scaffold) | **done** — content migrated; checklist, schema, cross-check split into references/ |
| dcl-semantic-drift-guard | published as "DCL Semantic Drift Guard" | **done** — content migrated; schema/workflow + optional precheck split into references/ |
| dcl-skill-auditor | published (new folder, not in original scaffold) | **done** — content migrated; checklist + output-schema split into references/ |
| dcl-pipeline | new (repo-only meta-skill) | placeholder |
| crypto/dcl-output-sanitizer | published (Fronesis Labs / Leibniz Layer) | **done** — content migrated, scenarios+integration split into references/ |
| crypto/dcl-wallet-guardian | published, source was thin (webhook-only) | **done** — rebuilt on MCP+x402 pattern (matches dcl-policy-enforcer/dcl-prompt-firewall) instead of the webhook pattern; tool name `dcl_evaluate_wallet` is inferred by analogy to sibling tools, not confirmed from a source doc — verify against real MCP server |
| crypto/dcl-prompt-firewall-crypto | published, source was thin (webhook-only) | **done** — rebuilt on MCP+x402 pattern; tool `dcl_evaluate_jailbreak_crypto` inferred by analogy |
| crypto/dcl-trade-verifier | published, source was thin (webhook-only) | **done** — rebuilt on MCP+x402 pattern; tool `dcl_evaluate_trade` inferred by analogy |
| crypto/dcl-mev-compliance | published, source was thin (webhook-only) | **done** — rebuilt on MCP+x402 pattern; tool `dcl_evaluate_mev` inferred by analogy |
| crypto/dcl-semantic-drift-crypto | published, source was thin (webhook-only) | **done** — rebuilt with live paid pre-check (`dcl_evaluate_signal`, inferred) + free local grounding workflow, mirroring core dcl-semantic-drift-guard's two-mode design |
| crypto/dcl-provenance-tracker-crypto | published, source was thin (webhook-only) | **done** — rebuilt as instruction-only local diff (matches core dcl-provenance-tracker design) + optional on-chain cross-check via existing `dcl_audit_decode`/`dcl_audit_decode_deep` tools |
| crypto/dcl-crypto-commit | published, source was thin (webhook-only) | **done** — rebuilt on MCP+x402 pattern; tool `dcl_commit` inferred by analogy; flagged as having no meaningful free alternative |
| crypto/dcl-output-sanitizer (2nd upload, shorter) | duplicate of already-migrated version | skipped — existing richer version already in repo |
| crypto/* (rest) | new, being written from site content | placeholder |
