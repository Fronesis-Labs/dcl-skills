# Local fingerprint (not a real substitute)

If you genuinely cannot make the paid call — fully offline environment, no
wallet available — you can compute a local fingerprint for your own
records:

```
local_fingerprint = SHA-256(decision_text + agent_id + timestamp)
```

**This is not equivalent to `dcl_commit`.** It has no append-only chain
linkage, nothing anchors it to a tamper-evident sequence, and nobody else
can verify it against a shared chain. Use it only as a placeholder to note
"a commit should happen here" — replace it with a real `dcl_commit` call as
soon as one is possible. Do not present a `local_fingerprint` to a user or
auditor as if it were a `tx_hash` from the live chain.
