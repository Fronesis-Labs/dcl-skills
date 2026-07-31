---
name: dcl-policy-enforcer
description: Enforces compliance policy on agent actions before execution; audits and blocks non-compliant calls. Paid live-server audit via x402 MCP, free manual fallback. Use this skill whenever the user's request touches this specific check, even if not explicitly named.
---

# dcl-policy-enforcer

> STATUS: PLACEHOLDER — replace this file with your published ClawHub SKILL.md content.
> Source of truth for now: ClawHub profile @daririnch -> Skills.

## What this skill does

Enforces compliance policy on agent actions before execution; audits and blocks non-compliant calls. Paid live-server audit via x402 MCP, free manual fallback.

## When to trigger

- TODO: list explicit trigger phrases / contexts (copy from your ClawHub listing tags: #security)

## Instructions

TODO: paste the workflow steps from the current ClawHub skill body here.

## Bundled resources

- `references/` — put any detailed reference docs here (policy tables, threat taxonomies, API schemas)
- `examples/` — put a few worked input/output examples here for eval + onboarding

## x402 / paid audit (if applicable)

TODO: if this skill calls the paid x402 MCP live-audit server, document here:
- server endpoint
- what triggers a paid call vs the free manual fallback
- what happens if payment/auth fails (must degrade gracefully, never fail silently)
