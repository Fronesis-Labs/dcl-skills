---
name: dcl-provenance-tracker
description: >
  Verify the integrity and version history of any ClawHub skill after an
  update. Compares two versions of a skill side-by-side, detects suspicious
  drift across 30+ known supply chain attack patterns, and returns a
  deterministic DCL provenance proof. 100% instruction-only for the diff
  itself — no external calls, no data leaves the agent. Optionally
  cross-check a past scan's on-chain integrity via the live DCL Trust Oracle
  MCP server. Use after every skill update, on a schedule for
  production-critical skills, or in CI/CD pipelines before agent deployment.
  Part of the DCL Skills security suite alongside DCL Skill Auditor, DCL
  Policy Enforcer, DCL Sentinel Trace, and DCL Semantic Drift Guard.
---

# DCL Provenance Tracker — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 1.1.0 · **Part of:** DCL Skills Security Suite

## What this skill does

Performs deterministic supply chain verification for ClawHub skills. It
compares two versions of a skill — a trusted baseline and a candidate
update — and detects behavioral drift, permission creep, and supply chain
attack patterns introduced between versions.

**The version-diff logic is 100% instruction-only.** No external network
calls are made for the comparison itself. No skill content leaves the
agent's context. The user provides both versions directly; the agent
analyzes them locally using the checklist in `references/drift-checklist.md`.
There is no live MCP tool that performs this specific diff — it's a
genuinely local, agent-side analysis.

## When to trigger

- Immediately after any `clawhub update` on a production skill
- On a schedule (daily/weekly) for business-critical skills
- Before agent deployment in CI/CD pipelines
- When a skill's behavior seems to have changed unexpectedly
- In combination with `dcl-skill-auditor` for full pre/post install coverage

## How to run a provenance check

The user provides both skill versions directly by pasting content into the
conversation. This makes **no network requests** and does not fetch content
from any external source.

**Getting the two versions:**
- **Baseline:** your saved copy of the previous SKILL.md, or download the
  prior version from ClawHub's version history before updating
- **Candidate:** the new version's SKILL.md after the update

### Step 1 — Confirm both versions are in context

Verify baseline and candidate SKILL.md are both present. If either is
missing, ask the user to paste them. Do **not** fetch from any URL.

### Step 2 — Compute version fingerprints

```
baseline_hash  = SHA-256(full baseline SKILL.md + all baseline scripts)
candidate_hash = SHA-256(full candidate SKILL.md + all candidate scripts)
```

If hashes are identical: verdict is `PASS`, no further analysis needed.

### Step 3 — Generate a structured diff

Identify added / removed / modified lines and sections. Focus on: scripts,
curl/bash commands, env var references, external URLs, permission
declarations, instruction text.

### Step 4 — Run the drift checklist

For each change, evaluate against categories D1–D5 in
`references/drift-checklist.md`. Record `severity`, `location`,
`change_type`, `snippet`, `description` for each finding.

### Step 5 — Apply verdict logic

| Condition | Verdict |
|---|---|
| Any `critical` finding | `BLOCK` |
| Two or more `major` findings | `BLOCK` |
| One `major` finding | `WARN` |
| Only `minor` findings | `WARN` |
| No findings | `PASS` |

### Step 6 — Compute DCL provenance proof

```
analysis_content  = verdict + risk_score + all findings (serialized)
analysis_hash     = SHA-256(analysis_content)
dcl_fingerprint   = "DCL-PT-" + date + "-" + candidate_hash[:8] + "-" + analysis_hash[:8]
```

Reproducible by anyone with the same two skill versions. Not submitted
anywhere by default — a local proof you can keep, share, or log yourself.

## Further reading

- `references/drift-checklist.md` — categories D1–D5 with full pattern lists
- `references/output-schema.md` — output JSON schema + PASS/BLOCK examples
- `references/onchain-crosscheck.md` — optional on-chain record lookup, integration patterns

## Privacy & data policy

Operated by Fronesis Labs. The version-diff logic is 100% instruction-only —
both skill versions are analyzed entirely within the agent's context window;
nothing is transmitted for the diff itself. The optional `dcl_audit_decode`
cross-check only ever retrieves metadata about a record you already
created — it does not receive either skill version as input.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-skill-auditor` — pre-install static security scanner (run before install)
- `dcl-policy-enforcer` — policy and jailbreak detection for AI outputs
- `dcl-sentinel-trace` — PII redaction and identity exposure detection
- `dcl-semantic-drift-guard` — hallucination and context drift detection
