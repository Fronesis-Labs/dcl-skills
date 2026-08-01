# Common exposure scenarios

These illustrate the kind of text `dcl_evaluate_wallet` (or the free
checklist) should catch — the redaction happens via `sanitized_output` /
manual redaction, not shown in detail here since the exact wording depends
on which path (live tool vs. free checklist) is used.

## Scenario 1 — Seed phrase pasted into a debugging session

A user pastes a wallet recovery phrase into chat while troubleshooting a
failed transaction. Left unredacted, it would sit in conversation history
and any downstream logs.

## Scenario 2 — Private key surfaced in agent reasoning

An agent debugging a signing script echoes the raw private key it loaded
from an env file back into its response.

## Scenario 3 — Wallet address leaked from RAG-retrieved content

A retrieved document (e.g. an internal ops runbook) contains a treasury
wallet address; the model quotes it verbatim in an otherwise public-facing
response.

## Scenario 4 — API key/bearer token in a wallet-integration context

A tool-calling agent reproduces the bearer token it used to call a wallet
custody API.
