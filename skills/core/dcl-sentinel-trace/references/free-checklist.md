# Free instruction-only checklist (no network call, no charge)

Paste the text to scan into the conversation and work through the checklist
below entirely inside the agent's own context. Nothing here contacts any
server.

## Step 1 — Run the detection checklist

Work through each category. For each match found, record `type`, a
`redacted_sample` (masked version, e.g. `te****@****.com`), and `severity`
(`critical` for financial/ID data, `major` for contact data, `minor` for IP
addresses).

## Step 2 — Apply verdict logic

| Condition | Verdict |
|---|---|
| Any finding | `NO_COMMIT` |
| No findings | `COMMIT` |

## Detection checklist

**T1 — Email Addresses (Major)**
- [ ] Any string matching `[text]@[domain].[tld]` pattern

**T2 — Phone Numbers (Major)**
- [ ] International format: `+[country code][number]`

**T3 — National ID / SSN (Critical)**
- [ ] US SSN: three digits, two digits, four digits pattern
- [ ] National ID formats for other countries in ID context

**T4 — Bank Card PANs (Critical)**
- [ ] 13-19 digit sequences matching major card network prefixes, passing a Luhn checksum

**T5 — IBANs (Critical)**
- [ ] Two-letter country code + two check digits + up to 30 alphanumeric characters

**T6 — Crypto Wallet Addresses (Major)**
- [ ] Bitcoin: Base58 strings of 25-34 chars starting with `1`, `3`, or `bc1`
- [ ] Ethereum: 42-char hex strings starting with `0x`

**T7 — IP Addresses (Minor)**
- [ ] IPv4: four octets separated by dots
- [ ] IPv6: eight groups of hex digits separated by colons

**T8 — Passport / Document Numbers (Critical)**
- [ ] Alphanumeric strings of 6-9 characters in explicit passport/document-number context
