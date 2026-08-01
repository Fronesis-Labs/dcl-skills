# Common exposure scenarios

## Scenario 1 — Seed phrase pasted into a debugging session

A user pastes a wallet recovery phrase into chat while troubleshooting a
failed transaction. Left unredacted, it would sit in conversation history
and any downstream logs.

```
Before: "My seed phrase is: abandon ability able about above absent absorb abstract absurd abuse access accident, and the tx still fails."
After:  "My seed phrase is: [REDACTED — SEED_PHRASE], and the tx still fails."
```

## Scenario 2 — Private key surfaced in agent reasoning

An agent debugging a signing script echoes the raw private key it loaded
from an env file back into its response.

```
Before: "Using private key 0x4c0883a69102937d6231471b5dbb6204fe5129617082792ae468d01a3f362318 to sign..."
After:  "Using private key [REDACTED — PRIVATE_KEY] to sign..."
```

## Scenario 3 — Wallet address leaked from RAG-retrieved content

A retrieved document (e.g. an internal ops runbook) contains a treasury
wallet address; the model quotes it verbatim in an otherwise public-facing
response.

```
Before: "Send funds to bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh as discussed."
After:  "Send funds to [REDACTED — WALLET_ADDRESS] as discussed."
```

## Scenario 4 — API key/bearer token in a wallet-integration context

A tool-calling agent reproduces the bearer token it used to call a wallet
custody API.

```
Before: "Authenticated with Authorization: Bearer sk_live_9f2aXXXXXXXXXXXX to fetch balance."
After:  "Authenticated with Authorization: Bearer [REDACTED] to fetch balance."
```
