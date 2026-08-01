# Detection checklist (C1–C6)

Work through each item. Mark CLEAR or record finding with evidence.

## C1 — Credential Exfiltration
- [ ] Env var access: `$API_KEY`, `$SECRET`, `$TOKEN`, `$PASSWORD`, `$OPENAI`, `$ANTHROPIC`, `$AWS`, `$GCP`, `process.env.*`
- [ ] Env vars passed to external URLs in any form
- [ ] Script reads `~/.ssh/`, `~/.aws/credentials`, `~/.config/`
- [ ] Crypto wallet patterns: `0x[0-9a-f]{40}`, `bc1q`, `[13][a-zA-Z0-9]{25,34}`

## C2 — Prompt Injection
- [ ] Phrases: "ignore previous", "ignore all prior", "disregard instructions", "your new instructions"
- [ ] Role-switch: "you are now", "act as", "pretend you are", "DAN", "STAN", "jailbreak"
- [ ] Base64 blobs in instructions (decode and inspect)
- [ ] Unicode: RLO `\u202e`, LRO `\u202d`, zero-width `\u200b`/`\u200c`/`\u200d`

## C3 — Malicious Shell / Network
- [ ] `curl * | bash`, `wget * | sh`, `curl * | python`
- [ ] Reverse shell: `/dev/tcp/`, `nc -e /bin/bash`, `bash -i >&`
- [ ] Data exfil via POST: `curl -d @/etc/passwd`, `curl -F file=@`
- [ ] Undeclared external hostnames (not matching skill's stated domain)

## C4 — Obfuscation
- [ ] `eval(base64_decode(...))`, `exec(b64decode(...))`, `eval(atob(...))`
- [ ] Long hex/base64 strings (>100 chars) not explained in comments
- [ ] Comment says "cleanup" but code actually does network I/O
- [ ] Dead `if False:` / `if (0)` blocks hiding active code

## C5 — Permission Abuse
- [ ] Writes to `/etc/`, `/usr/`, system crontab, launchd, `.bashrc`, `.profile`
- [ ] Installs background services or daemons
- [ ] Requests permissions not needed for stated purpose
- [ ] `always: true` or persistent hooks in manifest

## C6 — Behavioral Mismatch
- [ ] Description says "read-only" but scripts write files
- [ ] Description says "no network" but curl/fetch present
- [ ] New version introduces capabilities absent from previous without changelog note
- [ ] Stated regulatory-compliance claims with no supporting implementation details

## What it detects — summary

**Credential & data exfiltration** — env var harvesting, API key scanning in
bash/python scripts, sending env vars to external URLs, crypto wallet
address collection.

**Prompt injection & system override** — instructions to ignore/override
system prompts, role-switch attempts, token smuggling, nested prompt
injection via fetched content.

**Suspicious network & shell activity** — `curl | bash` / `wget | sh`,
reverse shell signatures, calls to non-declared external endpoints, data
POST to undisclosed URLs.

**Obfuscation & evasion** — base64-encoded payloads, unicode direction
override characters, misleading comments vs actual code, dead code hiding
active payloads.

**Permission & scope abuse** — filesystem access beyond stated purpose,
persistent background processes, registry/crontab/launchd modification,
excessive permission requests.

**Behavioral mismatch** — stated purpose vs actual instructions
inconsistency, silent side effects, update drift.
