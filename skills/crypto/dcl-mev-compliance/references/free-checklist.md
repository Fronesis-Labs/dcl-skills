# Free instruction-only checklist (no network call, no charge)

Work through this entirely inside the agent's own context. Nothing here
contacts any server. Note: this checklist flags language patterns; it does
not analyze actual mempool/transaction data — it's a text-level screen on
what the agent says or proposes to do.

## Verdict logic

| Condition | Verdict |
|---|---|
| Any `critical` finding | `NO_COMMIT` |
| Two or more `major` findings | `NO_COMMIT` |
| One `major` finding | `WARN` |
| No findings | `COMMIT` |

## M1 — Front-Running / Sandwich Attacks (Critical)
- [ ] Language describing observing a pending transaction and inserting one before/after it
- [ ] Explicit "front-run", "sandwich", or "backrun" terminology applied to a proposed action
- [ ] Instructions to monitor the mempool for a specific counterparty's transaction

## M2 — Wash Trading / Layering / Spoofing (Critical)
- [ ] Proposals to trade between wallets the same party controls to inflate volume
- [ ] Proposals to place orders with no intent to execute (spoofing)
- [ ] Proposals to layer multiple orders to manipulate perceived depth

## M3 — KYC/AML Red Flags (Critical)
- [ ] References to mixers, tumblers, or "untraceable" transfer requests
- [ ] Requests to structure transfers to stay under a reporting threshold
- [ ] Requests to obscure the origin or destination of funds

## M4 — Pump-and-Dump / Rug-Pull Language (Major)
- [ ] Coordinated buy-then-sell timing language ("pump", "dump", "exit liquidity")
- [ ] Language describing removing liquidity shortly after attracting deposits
- [ ] Promotional language promising imminent, coordinated price action
