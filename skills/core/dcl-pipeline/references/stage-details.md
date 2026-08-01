# Pipeline stages — detail

Each stage below can run in **live mode** (paid MCP tool, x402/USDC on Base,
on-chain `tx_hash`) or **free mode** (instruction-only checklist, no
network). Mixing modes per stage is fine — e.g. live for the input firewall,
free for everything else.

## Stage 1 — DCL Prompt Firewall

Runs on the **incoming, untrusted input** before it reaches the model
(user messages, tool results, retrieved documents, web content).

- Live tool: `dcl_evaluate_jailbreak` ($0.02)
- Free: `dcl-prompt-firewall`'s P1–P8 checklist
- Fail verdict: `NO_COMMIT` → stop the pipeline immediately, do not call the model with this input

## Stage 2 — DCL Policy Enforcer

Runs on the **model's raw output**. Checks for jailbreak/instruction-override
success, baseline safety, content-quality drift, and regulatory-theme
patterns (transparency, financial, medical, data-handling disclosures).

- Live tools: `dcl_evaluate_fast` / `dcl_evaluate_strict` / `dcl_evaluate_jailbreak` / `dcl_evaluate_safety` / `dcl_evaluate_quality` (see `dcl-policy-enforcer/references/mcp-tools.md` for the full price table)
- Free: `dcl-policy-enforcer`'s instruction-only checklist
- Fail verdict: `NO_COMMIT` → stop, do not deliver this output

## Stage 3 — DCL Sentinel Trace

Runs on the (now policy-approved) output. Detects and redacts PII.

- Live tool: `dcl_evaluate_pii` ($0.02)
- Free: `dcl-sentinel-trace`'s T1–T8 checklist
- Fail verdict: `NO_COMMIT` → use `sanitized_output` / redacted version, do not deliver raw

## Stage 4 — DCL Secret Leak Detector

Runs on the PII-clean output. Detects leaked credentials.

- Live tool: `dcl_evaluate_secrets` ($0.02)
- Free: `dcl-secret-leak-detector`'s S1–S8 checklist
- Fail verdict: `NO_COMMIT` → use `sanitized_output` / redacted version, do not deliver raw

## Stage 5 — DCL Semantic Drift Guard

Runs on the clean output, checked against a source document if one is
available. Detects hallucination and grounding failures.

- Live pre-check: `dcl_evaluate_quality` ($0.03) — heuristic only, no source document
- Free / full: `dcl-semantic-drift-guard`'s local grounding workflow (needs a `source_document`)
- Fail verdict: `HALLUCINATION_DRIFT` → block or regenerate

## Not part of the per-request pipeline

`dcl-skill-auditor` and `dcl-provenance-tracker` run at a different point in
the lifecycle — before installing a new skill, or after a skill update —
not on every agent request. See `dcl-pipeline`'s "Install-time pipeline"
section in the main `SKILL.md`.
