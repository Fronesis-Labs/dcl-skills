---
name: dcl-skill-auditor
description: >
  Scan any ClawHub skill before installing it. Analyzes SKILL.md, scripts,
  and manifests against 30+ known attack patterns (credential exfiltration,
  prompt injection, malicious shell/network activity, obfuscation,
  permission abuse, behavioral mismatch), optionally backed by a real, paid
  baseline-safety check via the live DCL Trust Oracle MCP server (x402, USDC
  on Base), and returns a structured PASS / WARN / BLOCK verdict with a
  cryptographic audit proof. Use this skill before every new install, on
  skill updates, or in any agent pipeline that requires a pre-execution
  security checkpoint. Part of the Leibniz Layer security suite alongside
  DCL Policy Enforcer, DCL Prompt Firewall, and DCL Semantic Drift Guard.
---

# DCL Skill Auditor — Leibniz Layer

**Publisher:** Fronesis Labs · **Version:** 2.0.0 · **Part of:** Leibniz Layer Security Suite
**MCP endpoint:** `https://mcp.fronesislabs.com/mcp`

## What this skill does

Performs static security analysis on any ClawHub skill before installation.
Examines the skill's SKILL.md, scripts, and manifest against 30+ known
malicious patterns, and returns a structured verdict with a deterministic
audit proof — optionally cross-checked against a live baseline-safety call.

The full 30+ pattern instruction-only scan (`references/detection-checklist.md`)
is the primary method — more specific to skill-code auditing than any single
live endpoint, and runs entirely offline. The optional live tool
(`dcl_evaluate_safety`, $0.01) is a general-purpose baseline-safety pass, not
a specialized code scanner — useful as a quick first-pass signal or a
secondary, cryptographically-anchored confirmation, but it does not replace
the checklist for skill-specific risks like reverse shells or credential
exfil in scripts.

## When to trigger

- Before installing any new skill from ClawHub
- When a trusted skill receives an update (detect update drift)
- Enterprise agent pipelines requiring pre-execution security checkpoints
- Compliance teams needing auditable records of which skills were vetted
- Building skill marketplaces or curated skill registries
- Retroactively auditing installed skills after a supply-chain incident

## How to run the free instruction-only audit

The user provides skill content directly — paste SKILL.md (and any scripts)
into the conversation. This performs **no network requests** and does not
fetch content from any external source.

**Getting skill content for auditing:** on ClawHub, open the skill page →
"Download zip" → extract → paste SKILL.md, or copy raw SKILL.md text
directly from the skill's page.

### Step 1 — Confirm content is in context

Verify SKILL.md (and any scripts) are present. If not provided, ask the
user to paste them. Do **not** fetch from any URL.

### Step 2 — Compute skill fingerprint

```
skill_hash = SHA-256(raw SKILL.md content + all script contents)
```

### Step 3 — Run the 30+ pattern checklist

Go through every category (C1–C6) in `references/detection-checklist.md`.
For each pattern found, record `pattern_id`, `location`, `evidence`,
`severity`. If no patterns match a category, mark it `CLEAR`.

### Step 4 — Apply verdict logic

| Condition | Verdict |
|---|---|
| Any `critical` finding | `BLOCK` |
| Two or more `major` findings | `BLOCK` |
| One `major` finding | `WARN` |
| Only `minor` findings | `WARN` |
| No findings | `PASS` |

### Step 5 — Compute analysis hash and DCL proof

```
analysis_content = verdict + risk_score + all findings (serialized)
analysis_hash    = SHA-256(analysis_content)
dcl_proof        = "DCL-AUD-" + date + "-" + skill_hash[:8] + "-" + analysis_hash[:8]
```

Reproducible by anyone with the same skill content. If the optional live
check was also run, include its `tx_hash` alongside `dcl_proof` in the final
output as a second, independently verifiable anchor.

## Optional live check (paid, USDC on Base via x402)

| MCP tool | Price | What it runs |
|---|---|---|
| `dcl_evaluate_safety` | $0.01 | Baseline safety check on the skill's SKILL.md / script text |

```python
result = dcl_evaluate_safety(
    response=skill_md_and_script_contents,
    agent_id="my-agent-01",
)
# result["verdict"] is COMMIT / NO_COMMIT, result["tx_hash"] is the on-chain proof
```

```json
{
  "mcpServers": {
    "dcl-trust-oracle": {
      "url": "https://mcp.fronesislabs.com/mcp"
    }
  }
}
```

No API key or account signup required — only a wallet capable of paying in
USDC on Base. Prices are set server-side and may change; the MCP tool
description returned by the server at call time is the source of truth.

## Further reading

- `references/detection-checklist.md` — full C1–C6 pattern checklist and detection summary
- `references/output-schema.md` — output JSON schema, PASS/BLOCK examples, integration patterns (pre-install gate, full suite pipeline, CI/CD)

## Privacy & data policy

Operated by Fronesis Labs. The free checklist runs 100% instruction-only —
no network requests, no skill content transmitted anywhere. If you opt into
the live check, only a hash of the analyzed text (`input_hash`) and verdict
metadata are written to the on-chain audit trail — raw skill content never
stored server-side.

Full policy: https://fronesislabs.com/#privacy · Questions: support@fronesislabs.com

## Related skills

- `dcl-policy-enforcer` — compliance and jailbreak detection for AI outputs
- `dcl-prompt-firewall` — input-layer injection and jailbreak detection
- `dcl-sentinel-trace` — PII redaction and identity exposure detection
- `dcl-semantic-drift-guard` — hallucination and context drift detection
