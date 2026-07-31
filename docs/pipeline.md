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
| dcl-policy-enforcer | published, x402 paid audit live | placeholder — paste content |
| dcl-sentinel-trace | published | placeholder — paste content |
| dcl-prompt-firewall | published | placeholder — paste content |
| dcl-secret-leak-detector | TODO confirm ClawHub slug | placeholder |
| dcl-semantic-drift-guard | published as "DCL Semantic Drift Guard" | placeholder — paste content |
| dcl-pipeline | new (repo-only meta-skill) | placeholder |
| crypto/* | new, being written from site content | placeholder |
