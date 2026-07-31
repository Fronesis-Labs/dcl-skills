# Security Model

TODO: describe threat model per skill category.

- **Trust boundary**: what's untrusted input (tool output, web content, on-chain data,
  user-uploaded files) vs trusted (the operator's own instructions).
- **Failure mode**: for each skill, what happens when it can't reach its paid audit
  server (x402) or a check is inconclusive — must default to fail-safe (block/flag),
  not fail-open (silently allow).
- **What each skill does NOT cover**: be explicit about scope limits so users don't
  over-trust a narrow check.

## x402 paid audit — failure handling

Document, per skill that uses it:
1. What the paid call actually verifies that the free path can't.
2. Exact behavior on payment failure / timeout / auth error (must not silently
   downgrade to "pass").
3. Where the audit result / receipt is logged for later review.
