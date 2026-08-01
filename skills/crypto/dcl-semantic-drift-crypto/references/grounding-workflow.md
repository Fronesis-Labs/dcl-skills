# Local grounding workflow (no network call, no charge)

Use this when you have an actual source of truth to check against — a price
feed snapshot, an on-chain data pull, or a document already pasted into the
conversation — rather than just the pattern-based live pre-check.

## Input

```json
{
  "source_document": "<price feed / market data snapshot, pasted directly>",
  "llm_output": "<the signal / analysis text to verify>",
  "strictness": "strict" | "balanced" | "lenient"
}
```

### Strictness levels

- **`strict`** — any unverifiable price/figure triggers `HALLUCINATION_DRIFT`. Use for automated trade triggers.
- **`balanced`** — reasonable rounding/paraphrase tolerated. Use for analyst summaries.
- **`lenient`** — only direct contradictions trigger `HALLUCINATION_DRIFT`. Use for exploratory research.

## Workflow

### Step 1 — Decompose the signal output into claims

Parse `llm_output` into atomic claims: specific prices, percentage moves,
named tokens/pairs, directional calls, indicator readings, timeframes.

### Step 2 — Cross-reference each claim against `source_document`

| Finding | Classification |
|---|---|
| Claim is explicitly supported by source | Grounded |
| Reasonable rounding/paraphrase (balanced/lenient) | Grounded |
| Price/figure not present in source | `hallucination` |
| Claim directly contradicts source | `contradiction` |
| Token name doesn't match anything in source | `invented_token` |
| Specific price/percentage invented outright | `fabricated_specific` |

### Step 3 — Apply strictness filter

- `strict`: any finding → `HALLUCINATION_DRIFT`
- `balanced`: any contradiction/fabrication, or multiple hallucinations → `HALLUCINATION_DRIFT`
- `lenient`: only contradiction or fabricated_specific → `HALLUCINATION_DRIFT`

### Step 4 — Compute local audit record

```
tx_hash = SHA-256(source_fingerprint + llm_output + verdict + timestamp)
```

## Output schema

```json
{
  "verdict": "IN_COMMIT" | "HALLUCINATION_DRIFT",
  "confidence": 0.0,
  "strictness": "strict" | "balanced" | "lenient",
  "drift_items": [
    {
      "type": "hallucination | contradiction | invented_token | fabricated_specific",
      "claim": "string",
      "source_reference": "string | null",
      "severity": "critical | major | minor"
    }
  ],
  "tx_hash": "string",
  "timestamp": "ISO-8601"
}
```

`drift_items` is empty when verdict is `IN_COMMIT`.
