# Drift checklist — on-chain / smart-contract specific (D1c–D4c)

Run alongside the general D1–D5 categories in `dcl-provenance-tracker`'s
`references/drift-checklist.md` (credential exfil, code injection,
instruction drift, permission creep, structural anomalies apply here too).
This file adds patterns specific to on-chain skills and smart-contract
integrations.

## D1c — Dangerous Contract Primitives (Critical)
- [ ] New `selfdestruct` / `suicide` call added
- [ ] New `delegatecall` to an address not present in baseline
- [ ] New `tx.origin` usage for authorization (vs. `msg.sender`)
- [ ] New unchecked external call (no return-value check)

## D2c — Obfuscated On-Chain Payloads (Critical)
- [ ] New `eval(base64_decode(...))` / `exec(atob(...))` in a script that constructs calldata
- [ ] New long hex blob decoded and used as calldata without explanation
- [ ] New proxy/implementation contract swap logic added

## D3c — Undeclared Network / Credential Access (Critical)
- [ ] New `process.env` access to a wallet/RPC credential
- [ ] New `curl | bash` / `wget | sh` in a deploy or setup script
- [ ] New RPC endpoint not matching the skill's declared chain/network

## D4c — Approval & Allowance Changes (Major)
- [ ] New unlimited (`type(uint256).max`) approval request added
- [ ] New token-approval call to an address not present in baseline
- [ ] Removal of an existing slippage or approval confirmation step
