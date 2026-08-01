---
name: dcl-pipeline
description: >
  Meta-skill that orchestrates the core DCL Skills as a single end-to-end
  compliance pipeline: DCL Prompt Firewall (input) → DCL Policy Enforcer →
  DCL Sentinel Trace → DCL Secret Leak Detector → DCL Semantic Drift Guard
  (output). Runs each stage in order, stopping or substituting a sanitized
  result the moment any stage returns a fail verdict, and returns one
  aggregated report instead of five separate ones. Each stage can run in
  live paid mode (x402 MCP tools) or free instruction-only mode, mixed as
  needed. Use this skill whenever a request needs full-pipeline compliance
  coverage rather than a single isolated check — for example, gating an
  entire agent turn end-to-end, or running a batch of past outputs through
  the full stack retroactively.
---

# DCL Pipeline — Leibniz Layer

Repo-only meta-skill (not published standalone on ClawHub) that chains the
five core DCL Skills into one orchestrated pass.

## What this skill does

Runs the standard DCL Skills stack, in order, on a single agent turn:

```
Untrusted input
        │
        ▼
DCL Prompt Firewall        ← blocks malicious input
        │ COMMIT
        ▼
      LLM call
        │
        ▼
DCL Policy Enforcer        ← jailbreak / safety / regulatory-pattern check on output
        │ COMMIT
        ▼
DCL Sentinel Trace         ← PII redaction
        │ COMMIT
        ▼
DCL Secret Leak Detector   ← credential scan
        │ COMMIT
        ▼
DCL Semantic Drift Guard   ← hallucination / grounding check
        │ IN_COMMIT
        ▼
Safe to deliver
```

This is the canonical stage order used consistently across all five
individual `SKILL.md` files' own pipeline diagrams — use it as the source
of truth if any other doc in this repo drifts from it.

## When to trigger

- Gating an entire agent turn end-to-end, rather than calling one check in isolation
- Batch-processing a set of past outputs through the full compliance stack retroactively
- Setting up a new agent pipeline and want the standard reference wiring
- User asks for "the full DCL check" / "run all the compliance skills" without naming one specifically

## How to run it

### Step 1 — Choose a mode per stage (or one mode for all)

Each stage independently supports **live** (paid MCP tool via x402) or
**free** (instruction-only checklist). Default to free unless the user has
indicated they want paid/on-chain-anchored verification, or a specific
stage's free checklist is insufficient for the content (e.g. Semantic Drift
Guard's full grounding check needs a `source_document`, which the live
pre-check doesn't require but is only a heuristic).

### Step 2 — Run Stage 1 (Prompt Firewall) on the input

Before calling the model at all. If `NO_COMMIT`: stop here, do not call the
model with this input. Report the block reason.

### Step 3 — Call the model

Only after Stage 1 passes.

### Step 4 — Run Stages 2–5 on the output, in order

For each stage: if it returns a fail verdict (`NO_COMMIT` /
`HALLUCINATION_DRIFT`), and the stage provides a `sanitized_output` /
redacted version, carry that forward as the working text for all subsequent
stages instead of the raw one. If a stage fails with no usable sanitized
alternative (e.g. Policy Enforcer's `NO_COMMIT` on a jailbroken response),
stop the pipeline and surface the failure — don't keep checking a response
you already know shouldn't be delivered.

### Step 5 — Aggregate and return one report

```json
{
  "pipeline_verdict": "COMMIT | NO_COMMIT | HALLUCINATION_DRIFT",
  "stages": {
    "prompt_firewall": "COMMIT | NO_COMMIT",
    "policy_enforcer": "COMMIT | NO_COMMIT | not_run",
    "sentinel_trace": "COMMIT | NO_COMMIT | not_run",
    "secret_leak_detector": "COMMIT | NO_COMMIT | not_run",
    "semantic_drift_guard": "IN_COMMIT | HALLUCINATION_DRIFT | not_run"
  },
  "tx_hashes": {
    "prompt_firewall": "string | null",
    "policy_enforcer": "string | null",
    "sentinel_trace": "string | null",
    "secret_leak_detector": "string | null",
    "semantic_drift_guard": "string | null"
  },
  "delivered_output": "string | null",
  "stopped_at": "string | null"
}
```

`stopped_at` names the stage that halted the pipeline, or `null` if all
five ran to completion. `tx_hashes` entries are `null` for any stage run in
free mode.

## Further reading

- `references/stage-details.md` — per-stage detail: what each stage checks, which live tool / free checklist it uses, what its fail verdict means for the pipeline
- `examples/worked-example.md` — a full worked run, including a variant where a mid-pipeline stage fails and gets sanitized rather than blocking outright

## Install-time pipeline (separate from the per-request pipeline above)

`dcl-skill-auditor` and `dcl-provenance-tracker` are not part of this
per-request pipeline — they run before installing a new DCL-ecosystem skill,
or after that skill receives an update:

```
New skill detected / update available
        │
        ▼
DCL Skill Auditor          ← is the skill itself safe to install?
        │ PASS
        ▼
DCL Provenance Tracker     ← did this update introduce new risks?
        │ PASS
        ▼
(then the per-request pipeline above applies to that skill's own output)
```

## Related skills

- `dcl-prompt-firewall` / `dcl-policy-enforcer` / `dcl-sentinel-trace` / `dcl-secret-leak-detector` / `dcl-semantic-drift-guard` — the five stages this skill orchestrates
- `dcl-skill-auditor` / `dcl-provenance-tracker` — install-time checks, run separately from this pipeline
