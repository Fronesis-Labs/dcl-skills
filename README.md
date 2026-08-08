# DCL Skills

**Don't trust the agent. Trust the proof.**

Compliance and security skills for AI agents — hallucination detection,
PII redaction, prompt-injection defense, secret-leak detection, and a
DeFi-specialized variant set for agents that touch wallets and on-chain
actions.

Published on [ClawHub](https://clawhub.ai/daririnch) under
[@daririnch](https://clawhub.ai/daririnch).

## Why This Exists

An agent that can act autonomously is also an agent that can be tricked,
drift off its instructions, or leak something it shouldn't — silently,
with no record. These skills give an agent (or the pipeline running it) a
way to check itself *before* acting and to leave a verifiable trail of what
it checked. Most skills work two ways: a free, instruction-only checklist
you can run yourself right now, and an optional live audit backed by the
DCL Trust Oracle for a cryptographically verifiable verdict.

## Structure

```
dcl-skills/
├── skills/
│   ├── core/      # general-purpose agent-safety skills
│   └── crypto/     # DeFi-specialized variants (wallets, trades, MEV, on-chain provenance)
├── docs/          # pipeline design, security model, whitepaper
├── tests/golden/  # golden-set eval cases per skill
└── .github/workflows/lint-skills.yml
```

## Skills

### Core

| Skill | Purpose |
|---|---|
| `dcl-policy-enforcer` | Enforce compliance policy on agent actions before execution |
| `dcl-sentinel-trace` | Detect & redact PII in agent outputs |
| `dcl-prompt-firewall` | Detect & block prompt injection |
| `dcl-secret-leak-detector` | Detect leaked secrets/credentials |
| `dcl-provenance-tracker` | Verify skill version integrity / detect supply-chain drift after updates |
| `dcl-skill-auditor` | Pre-install static security scanner for ClawHub skills |
| `dcl-semantic-drift-guard` | Detect hallucination / context drift vs grounding sources |
| `dcl-pipeline` | Meta-skill orchestrating the above as one pipeline |

### Crypto

| Skill | Purpose |
|---|---|
| `dcl-prompt-firewall-crypto` | Injection defense specialized for DeFi contexts |
| `dcl-wallet-guardian` | Validate wallet-affecting actions before execution |
| `dcl-trade-verifier` | Verify trade/swap parameters before execution |
| `dcl-mev-compliance` | Check MEV exposure / compliance before broadcast |
| `dcl-provenance-tracker-crypto` | Verify provenance of on-chain data/contracts referenced |
| `dcl-semantic-drift-crypto` | Crypto-specific hallucination detection (token/contract claims) |
| `dcl-output-sanitizer` | Sanitize agent output before display/execution |
| `dcl-crypto-commit` | Final compliance checkpoint before irreversible on-chain action |

## Free by Default, Verifiable if You Need It

Every skill runs standalone as a free, instruction-only checklist — no
account, no network call, no dependency on Fronesis Labs infrastructure.
Most also offer an optional live check against the DCL Trust Oracle for a
cryptographically sealed, tamper-evident verdict instead of a self-reported
one, metered per call (settled automatically via x402, USDC on Base) so
there's no subscription to manage for occasional or high-volume use.
`dcl-crypto-commit` is the one skill with no free equivalent, since its
whole purpose is writing to the on-chain audit chain. `dcl-provenance-tracker`
and `dcl-provenance-tracker-crypto` are the opposite case — 100%
instruction-only by design, with only an *optional* on-chain cross-check of
a prior record. See each skill's `SKILL.md` for details.

## Status

All 16 `SKILL.md` files under `skills/` (8 core + 8 crypto) have been
migrated from ClawHub and are complete — see
[docs/pipeline.md](docs/pipeline.md) for the per-skill migration log,
including notes on which MCP tool names/prices were inferred by analogy
rather than confirmed from a source doc (mainly the crypto suite, which
started from thin ClawHub listings).

## License

- **This repo**: Apache License 2.0 — see [LICENSE](LICENSE).
- **ClawHub listings**: platform-enforced MIT-0, cannot be overridden per
  skill.

See [docs/licensing.md](docs/licensing.md) for the full breakdown,
including an open item on x402/paid-audit wording vs ClawHub's
no-pricing-metadata rule.
