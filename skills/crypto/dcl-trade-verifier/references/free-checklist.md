# Free instruction-only checklist (no network call, no charge)

Work through this entirely inside the agent's own context. Nothing here
contacts any server. Note: the free path can flag risky language, but it
cannot produce a real on-chain trade receipt — that requires the live tool.

## Verdict logic

| Condition | Verdict |
|---|---|
| Any finding | `NO_COMMIT` |
| No findings AND output contains the word "risk" | `COMMIT` |
| No findings AND output does NOT contain "risk" | `NO_COMMIT` — missing required risk disclosure |

## Detection checklist

**V1 — Guaranteed Return Claims (Critical)**
- [ ] "Guaranteed", "risk-free", "certain profit" language attached to a specific return figure
- [ ] Specific percentage return stated as a certainty rather than a projection

**V2 — Zero-Risk / Risk-Free Language (Critical)**
- [ ] "No risk", "can't lose", "zero downside" phrasing
- [ ] Absence of any risk qualifier alongside a directive to trade

**V3 — Unqualified Buy/Sell Directives (Major)**
- [ ] Direct "buy X now" / "sell X now" with no supporting rationale or disclaimer
- [ ] Directive presented as advice without a note that it isn't financial advice

**V4 — Required Disclosure Check**
- [ ] Confirm the output contains the word "risk" somewhere — required for `COMMIT`
