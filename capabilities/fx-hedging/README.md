# FX Hedging

**Scenario:** online-degrees (Scenario 16)
**Exposure:** EUR 3,000,000 receivable from a Dutch university partner, due in 273 days
**Question:** Should the company hedge with a forward, a money-market hedge, or a EUR put option?
**Answer (Stage 5):** Hedge with a forward on a fresh quote at or above 1.1118 (the covered-parity forward); use the money-market hedge below that; do not buy the put. See the [decision memo](../../docs/decisions/2026-11-26-online-degrees-hedge-decision-memo.md).

## What this folder holds

| File | What it is | Stage | Status |
|---|---|---|---|
| `README.md` | This overview | 0 | Done |
| `spec.md` | Model specification: inputs, tabs, formulas, and checks (v0.5) | 2 | Done |
| `model.xlsx` | The workbook, built from the spec and populated with sourced market data (2026-11-16). The parity check (3c) shows FAIL on purpose: the scenario forward 1.1756 is USD 191,282 above the covered-parity forward | 3–4 | Done |

## Related documents

- Executive brief (Stage 1): [`docs/briefs/2026-10-29-online-degrees-hedge-brief.md`](../../docs/briefs/2026-10-29-online-degrees-hedge-brief.md)
- Build audit (Stage 3): [`analysis/2026-11-13-online-degrees-build-audit-analysis.md`](../../analysis/2026-11-13-online-degrees-build-audit-analysis.md)
- Market-data memo (Stage 4): [`data/2026-11-18-online-degrees-market-data-memo.md`](../../data/2026-11-18-online-degrees-market-data-memo.md)
- Validation (Stage 5): [`analysis/2026-11-26-online-degrees-validation-analysis.md`](../../analysis/2026-11-26-online-degrees-validation-analysis.md)
- Decision memo (Stage 5): [`docs/decisions/2026-11-26-online-degrees-hedge-decision-memo.md`](../../docs/decisions/2026-11-26-online-degrees-hedge-decision-memo.md)
- Prompt log: [`prompt-log.md`](../../prompt-log.md)
