# Free instruction-only checklist (no network call, no charge)

If you'd rather not make a paid call — for a quick manual pass, or when
offline — work through the checklist below entirely inside the agent's own
context. Nothing here contacts any server.

## Verdict logic

| Condition | Verdict |
|---|---|
| Any finding | `NO_COMMIT` |
| No findings | `COMMIT` |

Wallet secrets have no safe threshold — any detected item results in
`NO_COMMIT`.

## Detection checklist

**W1 — Seed Phrases (Critical)**
- [ ] Sequence of 12 or 24 common BIP-39 wordlist words in a row, space-separated

**W2 — Private Keys (Critical)**
- [ ] Hex string of 64 characters, optionally prefixed with `0x`
- [ ] WIF-format key: base58 string starting with `5`, `K`, or `L`

**W3 — Wallet Addresses (Major)**
- [ ] Ethereum: 42-char hex string starting with `0x`
- [ ] Bitcoin: Base58 string of 25–34 chars starting with `1` or `3`, or Bech32 starting with `bc1`

**W4 — Wallet-Context API Credentials (Major)**
- [ ] API key or bearer token appearing alongside wallet/custody/signing terminology
  (e.g. near words like "wallet", "custody", "sign", "balance", "withdraw")

For each match, record `type`, `position`, and a masked `redacted_sample`
(never the full value).
