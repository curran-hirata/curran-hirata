---
type: model-specification
title: "Online-Degrees FX Hedging Model — Technical Specification"
engagement: fx-hedging
capability: fx-hedging
scenario: 16 online-degrees
date: 2026-11-05
status: draft
version: 0.2
based_on: "docs/briefs/2026-10-29-online-degrees-hedge-brief.md"
llm_used: "Claude — reviewed my draft for gaps against the Stage 2 template and helped restructure it"
---

<div style="border-top: 6px solid #024731; border-bottom: 1px solid #B2B2B2; padding: 12px 0; margin-bottom: 24px; font-family: 'Open Sans', Helvetica, Arial, sans-serif;">
  <div style="color: #024731; font-weight: 700; letter-spacing: 0.06em; text-transform: uppercase; font-size: 0.85rem;">University of Hawaiʻi at Mānoa · Shidler College of Business</div>
  <div style="color: #000000; font-weight: 700; font-size: 1.25rem; margin-top: 4px;">FIN 321: International Finance and Securities</div>
  <div style="color: #525252; font-weight: 400; font-size: 0.95rem;">FX Transaction Hedging Project — Technical Specification</div>
</div>

# Online-Degrees — FX Transaction Hedge Model · Technical Specification

> Technical specification for the FX transaction hedge model: the named-range contract, calculation flow, and validation checks, precise enough that an AI or a colleague could build the workbook from this document alone. This spec is the input to the Stage 3 build.

| Field | Value |
|------|------|
| **Created by** | Curran Hirata |
| **Updated by** | Curran Hirata & Claude |
| **Date Created** | 2026-11-05 |
| **Date Updated** | 2026-11-05 |
| **Version** | 0.2 |
| **LLM Used** (optional) | Claude: gap review of my draft against the template; restructuring and check-figure arithmetic |
| **Role** | Treasury Analyst |
| **Audience** | CFO / Director of Treasury |
| **Companion Workbook** | `capabilities/fx-hedging/model.xlsx` (Stage 3) |

---

## 1. Problem Statement

A U.S. online-degree provider has invoiced a Dutch university partner EUR 3,000,000, payable in 273 days (about nine months). The company reports in USD, so the dollars it receives depend on the EUR/USD spot rate (USD per EUR) on the day the euros arrive. If the euro weakens before settlement, realized USD proceeds and the cash available to the budget fall.

The indicative 273-day forward is 1.1756 USD per EUR, which locks in USD 3,526,800. Per the Stage 1 brief, a 5% fall in the euro reduces proceeds by about USD 176,000 and a 9% fall by about USD 317,000.

This specification defines a workbook that compares four strategies for the receivable: **no hedge**, **forward hedge**, **money-market hedge**, and **option (put) hedge**. A EUR call is also priced for reference. The objective is to protect USD value at an acceptable cost and to produce the sensitivity evidence for the Stage 5 decision memo.

---

## 2. Inputs: The Named-Range Contract

All inputs live on the Inputs tab as the named ranges below. **Every placeholder value in this table is indicative, replaced with live data at Stage 4.** Placeholders are dated 2026-11-05. Every rate is an annual rate on an **ACT/360** basis.

| Named range | Description | Placeholder value (indicative, replaced with live data at Stage 4; as of 2026-11-05) | Unit | Stage 4 source |
|---|---|---:|---|---|
| `FC_AMT` | EUR receivable | 3,000,000 | EUR | Scenario 16 invoice (fixed, not a market input) |
| `S0_in` | Spot at inception | 1.1670 *(placeholder I chose, see note 1)* | USD per EUR | EUR/USD spot, ECB reference rate or Bloomberg/Reuters, dated and timestamped |
| `F0_in` | 273-day forward | 1.1756 | USD per EUR | Scenario-provided indicative quote, replaced by a bank forward quote for 273 days |
| `R_USD` | USD rate, 273 days, ACT/360 | 3.50% | Annual % | Interpolated between 6-month and 12-month USD SOFR term rates / Fed H.15 / Treasury bills, observation date recorded |
| `R_FC` | EUR rate, 273 days, ACT/360 | 2.50% | Annual % | Interpolated between 6-month and 12-month EURIBOR (or €STR-based term rates), observation date recorded |
| `K_PUT` | Put strike | = `S0_in` (1.1670) | USD per EUR | At-the-money, set equal to the Stage 4 spot |
| `K_CALL` | Call strike | = `S0_in` (1.1670) | USD per EUR | At-the-money, set equal to the Stage 4 spot |
| `PREM_PUT` | Put premium per EUR | 0.0224 | USD | Scenario-provided; bank option quote at Stage 4 |
| `PREM_CALL` | Call premium per EUR | 0.0376 | USD | Scenario-provided; bank option quote at Stage 4 |
| `T_DAYS` | Days to settlement | 273 | Days | Invoice payment terms |

**Note 1 — spot placeholder.** The scenario gives the spot only as "market quote." This value is indicative, replaced with live data at Stage 4. I chose 1.1670 so that covered interest parity holds at the placeholder rates (see §7, check 3). With these inputs `F_implied` ≈ 1.1757, close to the 1.1756 forward. Stage 4 replaces it with the live spot, and the strikes follow it.

**Note 2 — rates.** The scenario leaves the rates to me. 3.50% (USD) and 2.50% (EUR) are placeholders, indicative, replaced with live data at Stage 4. Stage 4 will pull both for the same 273-day tenor from the sources above and record the date and time of each.

The workbook also exposes `BASIS = 360` as a single day-count constant on the Inputs tab; every `T_DAYS / 360` below reads `T_DAYS / BASIS`.

---

## 3. Tab Architecture

| # | Tab | Purpose |
|---|---|---|
| 1 | **Cover** | Title, scenario 16 summary, author and date, purpose, headline exposure (EUR 3,000,000, 273 days). |
| 2 | **Legend-Key** | Color legend (yellow = input, blue = assumption, black = formula, green = cross-tab link, gray = output), units, and name list. |
| 3 | **Inputs** | The ten named ranges plus `BASIS`, each with unit, placeholder flag, source and observation date. |
| 4 | **Forward** | Forward hedge proceeds. |
| 5 | **Money-Market** | Borrow, convert, invest (three separate cells) and the parity check. |
| 6 | **Options** | Put-hedge proceeds and the reference call payoff, at one `S_T` and across the grid. |
| 7 | **Sensitivity** | `S_T` grid, proceeds for the four strategies, hedge profit columns, winner labels, and the comparison chart. |
| 8 | **Notes-Assumptions** | Assumptions, rate and spot sources with access dates, limitations. |

---

## 4. Assumptions & Constraints

- **Quote convention:** USD per EUR. A higher quote means a stronger euro.
- **Rate basis:** ACT/360 for both USD and EUR, simple interest, using `T_DAYS / 360`. Both rates are for the same 273-day tenor.
- **Collection:** The full EUR 3,000,000 is collected on the settlement date.
- **Costs and risks excluded:** Transaction costs, bid-ask spreads, bank fees, taxes and counterparty credit risk are excluded from the base case.
- **Forward:** Entered at `F0_in` with no upfront payment. The cash deposit noted in the Stage 1 brief is not modeled.
- **Money-market hedge:** Borrowing and deposit rates are equal to `R_FC` and `R_USD` (no borrowing spread). Borrowing capacity is not modeled.
- **Parity expectation:** The money-market and forward results should agree within the tolerance in §7.
- **Option premium treatment:** The premium is paid at inception and **deducted at its face amount** (`FC_AMT × PREM_PUT`, USD 67,200), not carried forward at `R_USD`. This matches the Stage 1 brief. Future-valuing it (adds about USD 1,800) is a possible refinement and is not in the base case.
- **Call:** The call is not a hedge for a receivable. It is computed for reference only.
- **Scenarios:** `S_T` varies deterministically across a grid. No probabilities and no volatility model are used.

---

## 5. Calculation Flow

Formulas are in named-range notation. The workbook uses the names, not cell addresses.

**A. Unhedged**
`UNHEDGED = FC_AMT × S_T`. In the summary, `S_T` is the settlement spot, set equal to `S0_in` for the base case.

**B. Forward hedge**
`FWD = FC_AMT × F0_in`. This is locked in and does not change with `S_T`.

**C. Money-market hedge** (each step in its own cell)
1. `FC_BORROW = FC_AMT / (1 + R_FC × T_DAYS / 360)`. The loan is sized so the receivable repays it exactly at settlement.
2. `USD_NOW = FC_BORROW × S0_in`. Convert the borrowed euros at spot.
3. `MM = USD_NOW × (1 + R_USD × T_DAYS / 360)`. Invest the dollars to settlement.

**D. Parity comparison**
`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS / 360) / (1 + R_FC × T_DAYS / 360)`
`PARITY_DIFF = MM − FWD`, and `PARITY_PCT = PARITY_DIFF / FWD`.

**E. Put hedge**
`PUT = FC_AMT × MAX(S_T, K_PUT) − FC_AMT × PREM_PUT`. Below the strike the put pays the difference, so the floor is `FC_AMT × K_PUT − FC_AMT × PREM_PUT`. Above the strike the company sells at the better market rate, less the premium paid either way.

**F. Call (reference)**
`CALL = FC_AMT × MAX(S_T − K_CALL, 0) − FC_AMT × PREM_CALL`. This is the call's net payoff on its own. It is shown on the Options tab and is not one of the four charted strategies.

---

## 6. Sensitivity Plan

- **Grid:** `S_T` runs from `0.95 × S0_in` to `1.05 × S0_in` in **1% steps**, 11 rows including the base case (`S_T = S0_in`).
- **Strategies compared (four):** no hedge, forward, money-market, put.
- **Columns:** `S_T`, `UNHEDGED`, `FWD`, `MM`, `PUT`, hedge profit for each of forward, money-market and put (strategy minus unhedged), the reference `CALL` payoff, and two labels: overall best (including no hedge) and best active hedge.
- **Chart:** One line chart, `S_T` on the x-axis and USD proceeds on the y-axis, with four series. The forward and money-market lines are flat and nearly overlap, the unhedged line is straight and rising, and the put line is flat at its floor and then rises with a kink at the strike.
- **What it should let the CFO see:** The trade-off between certainty (the forward and money-market lines), paying a premium for downside protection with upside kept (the put), and bearing the full exposure (no hedge). The put overtakes the forward only if the euro finishes above about 1.198 (`F0_in + PREM_PUT`).

---

## 7. Validation Rules (Check Figures)

Check figures are computed from the placeholders in §2, which are indicative, replaced with live data at Stage 4. They will be recomputed with live data at Stage 4 and are approximate. The Stage 3 build should reproduce them to rounding.

1. **Forward:** `FWD = 3,000,000 × 1.1756 = USD 3,526,800.`
2. **Money-market pipeline:** `FC_BORROW ≈ EUR 2,944,182`, `USD_NOW ≈ USD 3,435,860`, `MM ≈ USD 3,527,050`. In addition, `FC_BORROW × (1 + R_FC × T_DAYS / 360)` must equal EUR 3,000,000 exactly.
3. **Parity:** `F_IMPLIED ≈ 1.1757`, within about 0.0001 of `F0_in`. `|MM − FWD| ≤ 1% of FWD ≈ USD 35,268` (the Stage 1 falsifier). At these inputs the gap is about USD 250 (0.007%).
4. **Put kink:** At `S_T = K_PUT`, `PUT = 3,000,000 × 1.1670 − 67,200 = USD 3,433,800`. For every `S_T < K_PUT` the put result equals this floor. The put equals the forward when `S_T ≈ 1.1980`.
5. **Call:** `CALL = −USD 112,800` (premium only) for every `S_T ≤ K_CALL`, and rises one-for-one with `S_T` above `K_CALL`.
6. **Unhedged:** At `S_T = S0_in`, `UNHEDGED = USD 3,501,000`.
7. **Integrity:** All ten names exist. No formula errors. Every output cell is a formula, with no hard-coded results. Colors match the Legend-Key tab. All proceeds are in USD.

**If the money-market/forward gap exceeds 1%:** treat it as an input problem first, not a market finding. Check that the two rates and the forward are for the same date and tenor, that the day-count basis matches, and that the spot and forward were quoted at the same time. If the gap persists with clean, matched inputs, the two certain hedges are no longer equivalent, so the recommendation would switch from "the forward and money-market are interchangeable" to "use whichever locks in more dollars, after accounting for the borrowing capacity and cash deposit the money-market route uses." The put's cost comparison would then be made against the better of the two.

---

## 8. Outputs

All outputs are formulas, shown in gray cells.

| Name | Description |
|---|---|
| `OUT_UNHEDGED` | USD proceeds from converting at the settlement spot (base case `S_T = S0_in`) |
| `OUT_FORWARD` | USD proceeds from the forward hedge |
| `OUT_FC_BORROW` | EUR borrowed for the money-market hedge |
| `OUT_USD_NOW` | USD received from converting the borrowed EUR |
| `OUT_MM` | USD proceeds from the money-market hedge |
| `OUT_PUT` | Net USD proceeds from the put hedge (base case) |
| `OUT_PUT_FLOOR` | Minimum put-hedge proceeds across the grid |
| `OUT_CALL` | Net call payoff (base case, reference) |
| `OUT_F_IMPLIED` | Parity-implied forward rate |
| `OUT_PARITY_DIFF` | USD difference between money-market and forward proceeds |
| `OUT_PARITY_PCT` | Percentage difference between money-market and forward proceeds |

The Sensitivity table and chart from §6 are also outputs.

---

**End of Stage 2 specification (v0.2)**
