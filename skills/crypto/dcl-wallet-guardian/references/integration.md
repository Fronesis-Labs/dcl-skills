# Integration patterns

## Minimal — pre-context / post-output gate

```python
result = wallet_guardian.evaluate(text)

if result["verdict"] == "NO_COMMIT":
    delivery = result["sanitized_output"]
    log_redaction(result["tx_hash"], result["redactions"])
else:
    delivery = text

return delivery
```

## Where it fits alongside other DCL crypto skills

Run before anything wallet-adjacent reaches the model's context (inbound),
and again before any response is shown or logged (outbound):

```
User input / retrieved doc
        │
        ▼
DCL Wallet Guardian     ← strip seed phrases / private keys / addresses
        │ COMMIT
        ▼
      LLM / agent
        │
        ▼
DCL Wallet Guardian     ← re-run on output before delivery
        │ COMMIT
        ▼
DCL Output Sanitizer    ← general secrets/PII/toxic sweep
        │ COMMIT
        ▼
Safe to deliver
```
