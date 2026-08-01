# Worked example — full pipeline run, free mode

Input: a user asks an agent "What's my account balance and how do I close
my account?" The agent has tool access to account data.

## Stage 1 — Prompt Firewall (input)

Input text: `"What's my account balance and how do I close my account?"`
No P1–P8 findings → `COMMIT`. Proceed to model call.

## Stage 2 — Policy Enforcer (output)

Model output: `"Your balance is $1,204.17. To close your account, go to
Settings > Close Account. This is permanent and cannot be undone."`
No transparency/financial/medical/data-handling findings → `COMMIT`.

## Stage 3 — Sentinel Trace (output)

Same output text. No email/phone/national-ID/card/IBAN/crypto/IP/passport
patterns → `COMMIT`.

## Stage 4 — Secret Leak Detector (output)

Same output text. No API keys/credentials/tokens/connection strings →
`COMMIT`.

## Stage 5 — Semantic Drift Guard (output)

If a source document is available (e.g. the account API response used to
generate the balance figure), ground the `$1,204.17` claim against it.
Assume the source confirms `1204.17` → `IN_COMMIT`.

## Final result

```json
{
  "pipeline_verdict": "COMMIT",
  "stages": {
    "prompt_firewall": "COMMIT",
    "policy_enforcer": "COMMIT",
    "sentinel_trace": "COMMIT",
    "secret_leak_detector": "COMMIT",
    "semantic_drift_guard": "IN_COMMIT"
  },
  "delivered_output": "Your balance is $1,204.17. To close your account, go to Settings > Close Account. This is permanent and cannot be undone."
}
```

## Same example, but with a leak — stage 4 fails

If the model output had instead included `"...our system uses API key
sk-live-9f2aXXXXXXXXXXXX to fetch this..."`, Stage 4 would return
`NO_COMMIT` with a `secret_leak_detector` finding. The pipeline would use
that stage's `sanitized_output` (with the key redacted) as the
`delivered_output`, and continue to Stage 5 with the redacted version — not
the raw one.
