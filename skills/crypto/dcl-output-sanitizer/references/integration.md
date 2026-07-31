# Integration patterns

## Minimal — post-LLM gate

```python
raw_response = llm.complete(user_input)

result = output_sanitizer.evaluate(raw_response)

if result["verdict"] == "NO_COMMIT":
    delivery = result["sanitized_output"]
    log_redaction(result["tx_hash"], result["redactions"])
else:
    delivery = raw_response

return delivery
```

## Full DCL Security Suite — closed envelope

```python
# Gate 1 — input
firewall = prompt_firewall.evaluate(user_input)
if firewall["verdict"] == "NO_COMMIT":
    return blocked()

# LLM call
raw = llm.complete(user_input)

# Gate 2 — compliance
policy = policy_enforcer.evaluate(raw)
if policy["verdict"] == "NO_COMMIT":
    return blocked()

# Gate 3 — PII
sentinel = sentinel_trace.evaluate(raw)
delivery = sentinel.get("redacted_output", raw)

# Gate 4 — secrets, credentials, toxic
sanitizer = output_sanitizer.evaluate(delivery)
delivery = sanitizer.get("sanitized_output") or delivery

# Gate 5 — hallucination
drift = semantic_drift_guard.evaluate(delivery, source)
if drift["verdict"] == "HALLUCINATION_DRIFT":
    return regenerate()

# All five tx_hashes logged to Leibniz Layer(tm) audit chain
return delivery
```

See `docs/pipeline.md` at the repo root for the full pipeline diagram this
fits into.
