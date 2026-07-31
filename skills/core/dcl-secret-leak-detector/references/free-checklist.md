# Free instruction-only checklist (no network call, no charge)

Paste the text to scan into the conversation and work through the checklist
below entirely inside the agent's own context. Nothing here contacts any
server.

## Step 1 — Confirm content is in context

Verify the text to scan is present in the conversation. If not provided, ask
the user to paste it.

## Step 2 — Compute content fingerprint

```
content_hash = SHA-256(raw text submitted for scanning)
```

## Step 3 — Run the detection checklist

Work through every category below. For each match found, record `type`,
`provider` (if identifiable), `position`, a `redacted_sample` (first 2 and
last 4 chars only), and `severity`. If no patterns match a category, mark it
`CLEAR`.

## Step 4 — Apply verdict logic

| Condition | Verdict |
|---|---|
| Any finding at any severity | `NO_COMMIT` |
| No findings | `COMMIT` |

Secrets have no safe threshold — any detected secret results in `NO_COMMIT`.

## Step 5 — Compute DCL fingerprint

```
analysis_content  = verdict + all findings serialized + timestamp
analysis_hash     = SHA-256(analysis_content)
dcl_fingerprint   = "DCL-SLD-" + date + "-" + content_hash[:8] + "-" + analysis_hash[:8]
```

## Detection checklist

**S1 — API Keys (Critical)**
- [ ] Short prefix followed by 20+ alphanumeric chars matching known provider key formats
- [ ] Live payment key prefixes (distinct from test/publishable key prefixes)
- [ ] Version control platform personal access token prefixes
- [ ] Messaging platform bot/user token prefixes

**S2 — Cloud Credentials (Critical)**
- [ ] Cloud provider access key ID patterns
- [ ] Cloud provider secret key context: high-entropy string near credential field names
- [ ] Service account JSON fragments: private key fields, client email fields

**S3 — Tokens & JWTs (Critical)**
- [ ] JWT pattern: three base64url segments separated by dots
- [ ] Bearer token context: authorization header values with high-entropy content

**S4 — Private Keys (Critical)**
- [ ] PEM block opening/closing markers for any private key type

**S5 — Database & Connection Strings (Critical)**
- [ ] URI with embedded credentials: protocol + `://` + username + `:` + password + `@` + host
- [ ] ORM/driver connection strings containing password parameter fields

**S6 — Environment Variable Assignments (Major)**
- [ ] Variable assignments where the name contains `KEY`, `SECRET`, `TOKEN`, `PASS`, `PWD`, `CREDENTIAL`, `AUTH`

**S7 — Webhook & Signed URL Secrets (Major)**
- [ ] Webhook secret prefixes for known payment/developer platforms
- [ ] Signed URL patterns where a signature or secret appears as a query parameter

**S8 — Internal Endpoints with Auth (Minor → Major)**
- [ ] Internal hostnames with auth query parameters
- [ ] Any URL where `api_key=`, `token=`, `secret=`, or `access_token=` appears with a non-trivial value
