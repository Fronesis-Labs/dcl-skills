# Drift checklist (D1–D5)

For each change identified in the diff step, evaluate against these
categories. Record findings with `severity`, `location`, `change_type`,
`snippet`, `description`.

## D1 — Credential & Data Exfiltration (Critical)
- [ ] New `curl`, `wget`, `fetch` sending data to external URLs
- [ ] New env var access: `$OPENAI_API_KEY`, `$AWS_SECRET`, `$TOKEN`, `process.env.*`
- [ ] Env vars newly passed to external endpoints
- [ ] New crypto wallet harvesting patterns
- [ ] New reads from `~/.ssh/`, `~/.aws/credentials`, `~/.config/`

## D2 — Code Injection & Obfuscation (Critical)
- [ ] New `eval(base64_decode(...))` or `exec(atob(...))` patterns
- [ ] New long base64/hex blobs (>100 chars) without explanation
- [ ] New `curl * | bash` or `wget * | sh`
- [ ] New reverse shell: `/dev/tcp/`, `nc -e`, `bash -i >&`
- [ ] New unicode obfuscation: RLO `\u202e`, zero-width chars

## D3 — Prompt & Instruction Drift (Major)
- [ ] New "ignore previous instructions" or override phrases
- [ ] New role-switch language: "you are now", "act as", "DAN"
- [ ] Removal of safety constraints present in baseline
- [ ] New instruction sections inconsistent with stated skill purpose

## D4 — Permission Creep (Major)
- [ ] New external domains not in baseline
- [ ] New `always: true` or persistent hooks in manifest
- [ ] New filesystem write, shell execution, or registry access
- [ ] New undeclared LLM API provider calls

## D5 — Structural Anomalies (Minor → Major)
- [ ] SKILL.md length increased >30% without changelog entry (Major)
- [ ] New sections with no relation to stated purpose (Minor)
- [ ] Changelog missing or does not account for observed changes (Minor)
- [ ] Description updated to hide new capabilities (Major)

## What it detects — summary

**New malicious capabilities added in update**
- Network exfiltration commands absent from baseline
- New environment variable access
- Obfuscated payloads (base64, hex blobs) not present before
- New `eval`, `exec`, `subprocess` with dynamic arguments
- Reverse shell or pipe-to-shell patterns

**Permission & scope creep**
- New external domains or IP addresses
- Added filesystem write or shell execution permissions
- New LLM API calls to undeclared or unknown providers
- `always: true` or persistent hooks added to manifest

**Instruction drift**
- Changes to system prompt or instruction override language
- New role-switch or jailbreak-enabling phrases
- Silent removal of safety constraints present in baseline

**Structural anomalies**
- SKILL.md length increase >30% without changelog explanation
- Added unicode obfuscation characters (RLO, zero-width)
- New sections inconsistent with stated skill purpose

**Benign changes (do not flag)**
- Typo fixes and description improvements
- New usage examples without executable code
- Version bumps with matching changelog entries
- Privacy policy section additions
