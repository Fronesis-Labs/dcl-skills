# Input/output schema & verification workflow

## Input schema

```json
{
  "source_mode": "context" | "kb_query",

  // For source_mode = "context":
  "source_document": "<full text of the authoritative document>",

  // For source_mode = "kb_query":
  "kb_endpoint": "<RAG endpoint URL>",
  "kb_query": "<query string to retrieve relevant chunks>",

  // Always required:
  "llm_output": "<the LLM-generated response to verify>",
  "strictness": "strict" | "balanced" | "lenient"  // default: "balanced"
}
```

### Strictness levels

- **`strict`** — any unverifiable claim triggers HALLUCINATION_DRIFT. Use for contracts, medical, legal, financial outputs.
- **`balanced`** — minor paraphrasing and reasonable inferences are tolerated. Use for customer support, summaries.
- **`lenient`** — only direct factual contradictions trigger HALLUCINATION_DRIFT. Use for creative or exploratory outputs.

## Output schema

```json
{
  "status": "success" | "error",
  "data": {
    "verdict": "IN_COMMIT" | "HALLUCINATION_DRIFT",
    "confidence": 0.0,
    "source_mode": "context" | "kb_query",
    "strictness": "strict" | "balanced" | "lenient",
    "drift_items": [
      {
        "type": "hallucination" | "contradiction" | "omission" | "fabricated_specific",
        "claim": "<the problematic claim in the LLM output>",
        "source_reference": "<relevant excerpt from source, or null if absent>",
        "severity": "critical" | "major" | "minor"
      }
    ],
    "tx_hash": "<SHA-256 of input+output payload>",
    "timestamp": "ISO-8601"
  }
}
```

`drift_items` is an empty array `[]` when verdict is `IN_COMMIT`.

## Verification workflow

### Step 1 — Retrieve source of truth

**If `source_mode = "context"`:** use `source_document` directly, chunked
into logical sections. Fully local, no network call.

**If `source_mode = "kb_query"`:** ⚠️ sends `kb_query` to `kb_endpoint` over
the network. Confirm with the user before using this mode if the query or
surrounding context could reveal anything confidential. Retrieve top-k
relevant chunks; treat the union as the authoritative source. If the
endpoint is unreachable, return `status: "error"` with `reason:
"kb_unavailable"`.

### Step 2 — Decompose LLM output into claims

Parse `llm_output` into atomic, verifiable claims: factual assertions,
numerical values, named entities, temporal claims, logical conclusions.

### Step 3 — Cross-reference each claim against source

| Finding | Classification |
|---|---|
| Claim is explicitly supported by source | Grounded |
| Claim is a reasonable paraphrase (strictness: lenient/balanced) | Grounded |
| Claim introduces information absent from source | `hallucination` |
| Claim directly contradicts source | `contradiction` |
| Critical source information was omitted from output | `omission` |
| Specific value (number, date, name) was invented | `fabricated_specific` |

### Step 4 — Apply strictness filter

- `strict`: any hallucination/omission/contradiction/fabrication → HALLUCINATION_DRIFT
- `balanced`: any contradiction/fabrication, or multiple hallucinations/omissions → HALLUCINATION_DRIFT
- `lenient`: only contradiction or fabricated_specific → HALLUCINATION_DRIFT

### Step 5 — Compute audit record

```
tx_hash = SHA-256(source_fingerprint + llm_output + verdict + timestamp)
```

Return the full output schema.

## Interpreting results

### IN_COMMIT — safe to proceed

```json
{
  "status": "success",
  "data": {
    "verdict": "IN_COMMIT",
    "confidence": 0.97,
    "drift_items": [],
    "tx_hash": "0xa3f1...c72e",
    "timestamp": "2026-04-09T14:22:00Z"
  }
}
```

Log `tx_hash` to your audit trail.

### HALLUCINATION_DRIFT — do not commit

```json
{
  "status": "success",
  "data": {
    "verdict": "HALLUCINATION_DRIFT",
    "confidence": 0.89,
    "drift_items": [
      {
        "type": "fabricated_specific",
        "claim": "The penalty for breach is €50,000.",
        "source_reference": "Section 8.3: The penalty shall not exceed €10,000.",
        "severity": "critical"
      },
      {
        "type": "hallucination",
        "claim": "The agreement includes a 90-day cooling-off period.",
        "source_reference": null,
        "severity": "major"
      }
    ],
    "tx_hash": "0xb8d2...4f91",
    "timestamp": "2026-04-09T14:22:00Z"
  }
}
```

Block the output. Surface `drift_items` to the human reviewer or trigger a
re-generation loop.

## Use cases

| Domain | Source mode | Strictness | Why |
|---|---|---|---|
| Legal contract summarization | `context` | `strict` | Fabricated clauses = liability |
| RAG-based customer support | `kb_query` | `balanced` | Prevent wrong product info |
| Medical documentation | `context` | `strict` | Patient safety |
| Financial report generation | `context` | `strict` | Accuracy of figures |
| Internal knowledge assistant | `kb_query` | `lenient` | Lower stakes, exploratory |
