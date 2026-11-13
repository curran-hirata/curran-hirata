---
type: model-specification
title: "Online-Degrees FX Hedging Model — Technical Specification"
engagement: fx-hedging
capability: fx-hedging
scenario: 16 online-degrees
date: 2026-11-05
updated: 2026-11-13
status: draft
version: 0.4
based_on: "docs/briefs/2026-10-29-online-degrees-hedge-brief.md"
llm_used: "Claude — gap review of my draft against the Stage 2 template, restructuring, market-data lookup, check-figure arithmetic and the Stage 3 build audit"
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
| **Date Updated** | 2026-11-13 |
| **Version** | 0.4 |
| **LLM Used** (optional) | Claude: gap review of my draft against the template; restructuring; market-data lookup; check-figure arithmetic; Stage 3 build audit |
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

All inputs live on the Inputs tab as the named ranges below. **Every value in this table is indicative, replaced with live data at Stage 4.** Spot and both interest rates are market data observed on **2026-11-10** from the sources in §2.1 (retrieved 2026-11-13). The forward and the two option premiums are the scenario's indicative quotes and carry no market date. Every rate is an annual rate on an **ACT/360** basis.

| Named range | Description | Value (indicative, replaced with live data at Stage 4) | Unit | As of | Stage 4 source |
|---|---|---:|---|---|---|
| `FC_AMT` | EUR receivable | 3,000,000 | EUR | Scenario 16 invoice | Fixed, not a market input |
| `S0_in` | Spot at inception | 1.1214 | USD per EUR | 2026-11-10 | ECB euro reference rate (§2.1, source A), re-pulled and timestamped |
| `F0_in` | 273-day forward | 1.1756 | USD per EUR | Scenario quote, undated | Bank forward quote for 273 days, same date as the spot |
| `R_USD` | USD rate, 273 days | 4.41% | Annual %, ACT/360 | 2026-11-10 | Treasury 6-month and 1-year yields, interpolated (§2.1, sources B and C) |
| `R_FC` | EUR rate, 273 days | 3.12% | Annual %, ACT/360 | 2026-11-10 | Euribor 6-month and 12-month, interpolated (§2.1, source D) |
| `K_PUT` | Put strike | = `S0_in` (1.1214) | USD per EUR | 2026-11-10 | At-the-money: set equal to the spot |
| `K_CALL` | Call strike | = `S0_in` (1.1214) | USD per EUR | 2026-11-10 | At-the-money: set equal to the spot |
| `PREM_PUT` | Put premium per EUR | 0.0224 | USD | Scenario quote, undated | Bank option quote at Stage 4 |
| `PREM_CALL` | Call premium per EUR | 0.0376 | USD | Scenario quote, undated | Bank option quote at Stage 4 |
| `T_DAYS` | Days to settlement | 273 | Days | Scenario 16 invoice | Invoice payment terms |
| `BASIS` | Day-count denominator | 360 | Days | Convention | ACT/360 for both USD and EUR money-market rates |

Every `T_DAYS / BASIS` below equals `273 / 360 = 0.758333`.

**Grid and check settings** (blue assumption cells on the Inputs tab, named so no formula hard-codes them): `STEP_FRAC = 1%` (sensitivity step), `GRID_HALF_STEPS = 5` (steps each side of the base case, so the grid is ±5%), and `PARITY_TOL = 1%` (the Stage 1 falsifier threshold).

### 2.1 Market data sources and how the values were derived

Observation date for all market inputs: **2026-11-10**, the latest date on which the ECB rate, Euribor and Treasury yields were all published (the US Treasury published no yield for 2026-11-11, Veterans Day). Retrieved 2026-11-13.

| Ref | Input | Quote on 2026-11-10 | Source |
|---|---|---|---|
| A | EUR/USD spot | 1.1214 USD per EUR (ECB reference rate) | [ECB euro reference rate, US dollar](https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/eurofxref-graph-usd.en.html) |
| B | USD 6-month Treasury | 4.38% | [Federal Reserve H.15, Selected Interest Rates](https://www.federalreserve.gov/releases/h15/); cross-check: [Treasury daily par yield curve](https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve&field_tdr_date_value=2026) |
| C | USD 1-year Treasury | 4.56% | Same two pages as B |
| D | EUR 6-month and 12-month Euribor | 3.018% and 3.217% (ACT/360) | [Euribor rates, Bank of Finland](https://www.suomenpankki.fi/en/statistics/interest-rates-and-exchange-rates/euribor-rates/) (rates owned by the European Money Markets Institute) |

**Interpolation to 273 days.** The weight on the longer tenor is `(273 − 182) / (365 − 182) = 91 / 183 = 0.4973`.

- **USD:** `4.38% + 0.4973 × (4.56% − 4.38%) = 4.470%` on a bond-equivalent (ACT/365) basis. Converted to ACT/360 by multiplying by `360 / 365`: **4.408%, entered as `R_USD` = 4.41%.**
- **EUR:** `3.018% + 0.4973 × (3.217% − 3.018%) = 3.117%`, already ACT/360, **entered as `R_FC` = 3.12%.**

**Note 1 — timing of the quotes.** The ECB rate is fixed at 14:15 CET, Treasury yields are end-of-day, and Euribor is a morning fixing, so the three are not simultaneous. That is acceptable for a spec. Stage 4 takes the spot, rates and bank quotes from the same date and time, and records both.

**Note 2 — the forward is not consistent with this spot.** The scenario's forward, 1.1756, is 0.0542 (4.83%) above the 1.1214 spot. The rates above imply a 273-day forward of only about 1.1321 (+0.95%). The day count is not the cause: switching `BASIS` to 365 moves the gap by about 0.01 percentage points. To justify the forward at this spot, the USD rate would have to be about 9.6% with the EUR rate at 3.12% (a spread of about 6.5 points, against 1.3 points in the market). The scenario's forward was not quoted on the same date as these market inputs, and the spec keeps `F0_in` as given. The consequence is worked through in §7, check 3.

**Note 3 — the scenario quotes look jointly stale.** The forward and the rates imply a spot of about 1.1645, and the forward and put-call parity on the two premiums (strikes at the money) imply an at-the-money spot of about 1.1599 (§7, check 9). Both sit about 3.5% above the 1.1214 ECB spot. The forward and both premiums are undated scenario quotes, so they may all have been struck at an earlier, higher spot. With the strikes set to 1.1214, the premiums may not match an at-the-money option today. Stage 4 re-quotes the forward and both premiums on the same date and time as the spot.

---

## 3. Tab Architecture

| # | Tab | Purpose |
|---|---|---|
| 1 | **Cover** | Title, scenario 16 summary, author and date, purpose, headline exposure (EUR 3,000,000, 273 days). |
| 2 | **Legend-Key** | Color legend (yellow = input, blue = assumption, green = formula, including cross-tab links, gray = output), units, and name list. |
| 3 | **Inputs** | The ten named ranges plus `BASIS`, each with unit, value, as-of date, source and the "indicative, replaced with live data at Stage 4" flag; the grid and check settings; and the market quotes with the interpolation to 273 days (§2.1) as formulas. |
| 4 | **Forward** | Forward hedge proceeds. |
| 5 | **Money-Market** | Borrow, convert, invest (three separate cells) and the parity check. |
| 6 | **Options** | Put-hedge proceeds and the reference call payoff, at one `S_T` and across the grid, including the future-valued premium. |
| 7 | **Sensitivity** | `S_T` grid, proceeds for the four strategies, hedge profit columns, winner labels, and the comparison chart. |
| 8 | **Notes-Assumptions** | Assumptions, the §2.1 source table with access dates, limitations. |
| 9 | **Checks** | Every §7 check figure computed live beside the model value, with a PASS or FAIL status, and the cross-checks in §7, check 9. A FAIL is reported, never forced to pass. |

---

## 4. Assumptions & Constraints

- **Quote convention:** USD per EUR. A higher quote means a stronger euro.
- **Rate basis:** ACT/360 for both USD and EUR, simple interest, using `T_DAYS / BASIS`. Both rates are for the same 273-day tenor. The USD Treasury yield is converted from its ACT/365 bond-equivalent basis to ACT/360 (§2.1).
- **Collection:** The full EUR 3,000,000 is collected on the settlement date.
- **Costs and risks excluded:** Transaction costs, bid-ask spreads, bank fees, taxes and counterparty credit risk are excluded from the base case.
- **Forward:** Entered at `F0_in` with no upfront payment. The cash deposit noted in the Stage 1 brief is not modeled.
- **Money-market hedge:** Borrowing and deposit rates are equal to `R_FC` and `R_USD` (no borrowing spread). Borrowing capacity is not modeled.
- **Parity expectation:** The money-market and forward results should agree within the tolerance in §7. At the current inputs they do not (§7, check 3).
- **Option premium timing and treatment:** The put premium is paid on day 0, at inception, and the euros arrive on day 273. A dollar paid on day 0 is not worth a dollar on day 273: it could have earned the USD deposit rate. `R_USD` carries it across, so the premium is **future-valued to settlement**: `PUT_PREM_FV = FC_AMT × PREM_PUT × (1 + R_USD × T_DAYS / BASIS)`. At the current inputs, the cash paid today is `PUT_PREM_CASH = FC_AMT × PREM_PUT = USD 67,200` and its settlement-date value is **USD 69,447**, USD 2,247 more. The call premium is treated the same way. The base case uses the future-valued figure, which puts the option on the same footing as the other strategies, all measured at day 273. The Stage 1 brief quoted USD 67,200, which is the cash paid today and remains correct as that figure.
- **Effect of premium timing on the grid:** It changes no winner in any row. It does reverse the ranking of the put and the money-market hedge at `S_T = 1.1550` (+3%): with the face premium the put is USD 1,576 ahead, and with the future-valued premium it is USD 671 behind.
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
1. `FC_BORROW = FC_AMT / (1 + R_FC × T_DAYS / BASIS)`. The loan is sized so the receivable repays it exactly at settlement.
2. `USD_NOW = FC_BORROW × S0_in`. Convert the borrowed euros at spot.
3. `MM = USD_NOW × (1 + R_USD × T_DAYS / BASIS)`. Invest the dollars to settlement.

**D. Parity comparison**
`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS / BASIS) / (1 + R_FC × T_DAYS / BASIS)`
`PARITY_DIFF = MM − FWD`
`PARITY_PCT = PARITY_DIFF / FWD`
`PARITY_FLAG` shows PASS when `ABS(PARITY_PCT) ≤ 1%` and FAIL otherwise.

**E. Put hedge**
`PUT_PREM_FV = FC_AMT × PREM_PUT × (1 + R_USD × T_DAYS / BASIS)`
`PUT = FC_AMT × MAX(S_T, K_PUT) − PUT_PREM_FV`. Below the strike the put pays the difference, so the floor is `FC_AMT × K_PUT − PUT_PREM_FV`. Above the strike the company sells at the better market rate, less the premium paid either way.

**F. Call (reference)**
`CALL = FC_AMT × MAX(S_T − K_CALL, 0) − FC_AMT × PREM_CALL × (1 + R_USD × T_DAYS / BASIS)`. This is the call's net payoff on its own. It is shown on the Options tab and is not one of the four charted strategies.

---

## 6. Sensitivity Plan

- **Grid:** `S_T` runs from `0.95 × S0_in` to `1.05 × S0_in` in **1% steps**, 11 rows including the base case (`S_T = S0_in`). The first row is `S0_in × (1 − GRID_HALF_STEPS × STEP_FRAC)` and each later row adds `STEP_FRAC`; no row is typed. At the current spot, that is 1.0653 to 1.1775.
- **Strategies compared (four):** no hedge, forward, money-market, put.
- **Columns:** `S_T`, `UNHEDGED`, `FWD`, `MM`, `PUT`, hedge profit for each of forward, money-market and put (strategy minus unhedged), the reference `CALL` payoff, and two labels: overall best (including no hedge) and best active hedge.
- **Chart:** One line chart, `S_T` on the x-axis and USD proceeds on the y-axis, with four series. The forward and money-market lines are flat, with the money-market line below the forward at the current inputs. The unhedged line is straight and rising, and the put line is flat at its floor and then rises with a kink at the strike.
- **What it should let the CFO see:** The trade-off between certainty (the forward and money-market lines), paying a premium for downside protection with upside kept (the put), and bearing the full exposure (no hedge). The put overtakes the forward only above `F0_in + PUT_PREM_FV / FC_AMT`, about 1.1987, which is above the top of the grid (1.1775). On this grid the put never beats the forward, and the unhedged line crosses the forward only in the last row.

---

## 7. Validation Rules (Check Figures)

Check figures are computed from the values in §2, which are indicative, replaced with live data at Stage 4. They will be recomputed with live data at Stage 4 and are approximate. The Stage 3 build should reproduce them to rounding. These are base-case regression values for the inputs in §2: if any input changes, the expected figures on the Checks tab must be recomputed. Checks 2d, 3b and 4b (loan repayment, `FC_AMT × F_IMPLIED = MM`, put floor) hold for any inputs.

1. **Forward:** `FWD = 3,000,000 × 1.1756 = USD 3,526,800.`
2. **Money-market pipeline:** `FC_BORROW ≈ EUR 2,930,661`, `USD_NOW ≈ USD 3,286,443`, `MM ≈ USD 3,396,350`. In addition, `FC_BORROW × (1 + R_FC × T_DAYS / BASIS)` must equal EUR 3,000,000 exactly.
3. **Parity:** `F_IMPLIED ≈ 1.1321`. Identity check, which always passes: `FC_AMT × F_IMPLIED` equals `MM` to the cent (about USD 3,396,350). Threshold check (the Stage 1 falsifier): `|MM − FWD|` must be within 1% of `FWD`, about USD 35,268. **At these inputs it is not:** `PARITY_DIFF ≈ −USD 130,450` (`PARITY_PCT ≈ −3.70%`), so `PARITY_FLAG` must show FAIL. The workbook must display that result, not hide it. The cause is the forward (§2.1, note 2).
4. **Put kink:** At `S_T = K_PUT`, `PUT = 3,000,000 × 1.1214 − 69,447 = USD 3,294,753`. For every `S_T < K_PUT` the put result equals this floor. The put equals the forward when `S_T ≈ 1.1987`, above the grid.
5. **Call:** `CALL = −USD 116,572` (future-valued premium only) for every `S_T ≤ K_CALL`, and rises one-for-one with `S_T` (times `FC_AMT`) above `K_CALL`.
6. **Unhedged:** At `S_T = S0_in`, `UNHEDGED = USD 3,364,200`.
7. **Grid winners:** The forward is the best strategy in rows −5% to +4%. In the +5% row (`S_T = 1.1775`) unhedged proceeds of USD 3,532,410 beat the forward's USD 3,526,800 by USD 5,610. The best active hedge is the forward in all 11 rows.
8. **Integrity:** All eleven names exist (ten plus `BASIS`). No formula errors. Every output cell is a formula, with no hard-coded results. Colors match the Legend-Key tab. All proceeds are in USD.
9. **Cross-checks (informational, no pass or fail except 9a and 9b):** (a) `R_USD` and `R_FC` equal the interpolated derivations in §2.1 rounded to 4 decimals. (b) The forward implied by the premiums, `K_PUT + (PREM_CALL − PREM_PUT) × (1 + R_USD × T_DAYS / BASIS)`, is about 1.1371, within 0.005 of the rate-implied forward (1.1321) and 0.038 below `F0_in`. (c) The spot implied by `F0_in` and the rates, `F0_in × DF_FC / DF_USD`, is about 1.1645. (d) The at-the-money spot implied by `F0_in` and the premiums, `F0_in − (PREM_CALL − PREM_PUT) × DF_USD`, is about 1.1599. Checks (c) and (d) show the scenario quotes pointing to a spot near 1.16, not the observed 1.1214 (§2.1, note 3).

**If the money-market/forward gap exceeds 1%:** treat it as an input problem first, not a market finding. Check that the two rates and the forward are for the same date and tenor, that the day-count basis matches, and that the spot and forward were quoted at the same time. Here the rates share one tenor and date, both are ACT/360, and the spot is from the same day, so the unmatched input is the scenario's forward, which has no market date. The first action at Stage 4 is to replace `F0_in` with a bank quote for 273 days taken at the same time as the spot. In the workbook, setting `F0_in` to the rate-implied 1.1321 makes the parity flag PASS, which confirms the forward is the only unmatched input. If that quote comes back near 1.132, parity holds and the forward and money-market hedge are interchangeable, which is the Stage 1 expectation. If the gap persists with clean, matched inputs, the two certain hedges are no longer equivalent. The recommendation would then switch from "the forward and money-market are interchangeable" to "use whichever locks in more dollars, after accounting for the borrowing capacity and cash deposit the money-market route uses." On the scenario's forward taken at face value, that is the forward, by about USD 130,450, but a gap that size would be treated as a stale quote until confirmed by the bank. The put's cost comparison would then be made against the better of the two.

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
| `OUT_PUT_PREM_FV` | Put premium future-valued to settlement |
| `OUT_PUT` | Net USD proceeds from the put hedge (base case) |
| `OUT_PUT_FLOOR` | Minimum put-hedge proceeds across the grid |
| `OUT_CALL` | Net call payoff (base case, reference) |
| `OUT_F_IMPLIED` | Parity-implied forward rate |
| `OUT_PARITY_DIFF` | USD difference between money-market and forward proceeds |
| `OUT_PARITY_PCT` | Percentage difference between money-market and forward proceeds |
| `OUT_PARITY_FLAG` | PASS or FAIL against the 1% threshold |

The Sensitivity table and chart from §6 are also outputs.

---

## Appendix A — Change Log

| Version | Date | Author | Change |
|---|---|---|---|
| 0.2 | 2026-11-05 | Curran Hirata & Claude | Rewritten on the Stage 2 template with scenario 16 inputs; placeholders flagged. |
| 0.3 | 2026-11-13 | Curran Hirata & Claude | Replaced the chosen spot and rates with sourced, dated market values (§2.1); strikes follow the spot; added `BASIS` row; put and call premiums future-valued at `R_USD` (§4); all check figures recomputed; parity check now reports FAIL against the scenario forward. |
| 0.4 | 2026-11-13 | Curran Hirata & Claude | Stage 3 audit fixes: Legend-Key colors aligned with the build contract (green = formula); added the Checks tab (§3); named the grid and check settings (§2, §6); added the base-case regression note and the cross-checks to §7; added notes 2 and 3 on the scenario forward and premiums. |

---

**End of Stage 2 specification (v0.4)**
