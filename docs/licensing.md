# Licensing

## GitHub (this repo) — Apache License 2.0

This repository, `Fronesis-Labs/dcl-skills`, is the canonical source and is
licensed under **Apache 2.0** (see [LICENSE](../LICENSE)). Copyright holder:
Fronesis Labs.

## ClawHub — MIT-0 (platform-enforced, no override possible)

Per ClawHub's own docs (`skill-format.md`), every skill published to ClawHub
is automatically licensed under **MIT-0**, regardless of what a `SKILL.md`
says. Attribution is not required and the platform does not support
per-skill license overrides. Do not put `License: Apache 2.0` (or any other
license line) in a `SKILL.md` destined for ClawHub — it will be ignored /
flagged as conflicting.

Instead, each published `SKILL.md` should point back to this repo as the
canonical source, e.g.:

```
Canonical source: https://github.com/Fronesis-Labs/dcl-skills (Apache-2.0)
Published on ClawHub under the platform's MIT-0 terms.
```

## ⚠️ Open question: paid / x402 audit skills

ClawHub's docs also state it does **not support paid skills, per-skill
pricing, paywalls, or revenue sharing**, and that pricing metadata in
`SKILL.md` is not part of the skill format. This is separate from the
license question but affects the same skills — anything on ClawHub framed
as a "paid audit" (currently `dcl-policy-enforcer` and any others using the
x402 MCP flow) should be reviewed before/after publishing:

- The x402 payment call itself, as a runtime *behavior* the skill performs,
  is presumably fine — that's just what the skill does when it runs locally
  in the user's own agent.
- What to avoid: describing it in SKILL.md in a way that reads as ClawHub
  itself gating or charging for the skill (pricing tiers, "$X per audit",
  paywall language framed as part of the listing rather than the external
  service).
- TODO: re-read the wording in each x402-enabled `SKILL.md` before
  publishing/republishing and adjust if it reads as pricing metadata rather
  than a description of an external paid API the skill happens to call.
