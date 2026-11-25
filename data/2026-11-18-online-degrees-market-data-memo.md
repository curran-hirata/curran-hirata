# Market-data memo — online-degrees (scenario 16)

**Stage 4: Market Data + Population**
**Date begun:** 2026-11-18
**Market data as of:** close of **2026-11-16** (the latest date on which the ECB rate, the Federal Reserve H.15 yields and Euribor were all published)
**Retrieved:** 2026-11-18 00:35 HST (10:35 UTC)
**Workbook:** `capabilities/fx-hedging/model.xlsx` (populated through the named-range input cells)
**Prepared by:** Curran Hirata, with Claude (data retrieval, recomputation and workbook fixes)

## 1. Summary

- Spot, the USD rate and the EUR rate are now live market values for 2026-11-16, each with a source and timestamp (§2). The forward and both option premiums are the scenario's quotes, kept as given.
- **The parity check fails, and I did not change any input to make it pass.** The scenario forward of 1.1756 is 0.0638 USD per EUR above the covered-interest-parity (CIP) forward of 1.1118. On the EUR 3,000,000 receivable that is **USD 191,282**. The money-market hedge returns USD 3,335,518, which is 5.42% below the forward proceeds of USD 3,526,800 and well outside the 1% tolerance (USD 35,268).
- The gap is larger than the 3.70% I reported at Stage 2 because the euro fell from 1.1214 (2026-11-10) to 1.1014 (2026-11-16) while the scenario forward stayed at 1.1756.
- Populating the workbook exposed three structural defects, now fixed in separate commits (§6). Nothing else needed to change.
- The FX Hedging Lab agrees with the workbook on the forward, the three money-market steps, the implied forward and the parity gap, to the dollar (§7). The put and the sensitivity grid were compared too: the unhedged, forward and money-market columns agree in all 11 rows, and the put differs by a constant USD 2,272.82 because the Lab deducts the premium at face value while the workbook carries it to day 273 (§7).

## 2. Inputs, sources and timestamps

| Input | Value | Source | Timestamp | Proxy or computation |
|---|---|---|---|---|
| `S0_in` (EUR/USD spot) | **1.1014** USD per EUR | [ECB euro reference rate, US dollar](https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/eurofxref-graph-usd.en.html) | Reference rate for 2026-11-16 (fixed 14:15 CET), retrieved 2026-11-18 00:35 HST | None. Cross-check below. |
| `R_USD` (273-day USD rate) | **4.46%**, ACT/360 | US Treasury constant-maturity yields, [Federal Reserve H.15](https://federalreserve.gov/releases/h15/) (release dated 2026-11-17): 6-month **4.42%**, 1-year **4.62%** for 2026-11-16 | Data for 2026-11-16 close, retrieved 2026-11-18 00:35 HST | Interpolated to 273 days: weight `(273 − 182) / (365 − 182) = 0.4973`, so 4.42% + 0.4973 × 0.20% = 4.519% (bond-equivalent, ACT/365). Converted to ACT/360 by × 360/365 = 4.458%, entered as 4.46%. |
| `R_FC` (273-day EUR rate) | **3.18%**, ACT/360 | Euribor (European Money Markets Institute), via the [Bank of Finland Euribor page](https://www.suomenpankki.fi/en/statistics/interest-rates-and-exchange-rates/euribor-rates/): 6-month **3.008%**, 12-month **3.350%** for 2026-11-16 | Fixing for 2026-11-16 (the page publishes with a 24-hour delay), retrieved 2026-11-18 00:35 HST | Interpolated with the same weight: 3.008% + 0.4973 × 0.342% = 3.178%, entered as 3.18%. Already ACT/360. |
| `K_PUT`, `K_CALL` | **1.1014** (= `S0_in`) | Scenario convention: at the money | 2026-11-16 | Formula `=S0_in` in the workbook, so the strikes follow the spot. |
| `F0_in` (273-day forward) | **1.1756** | The scenario's bank quote | Undated (no market date given) | **Kept as given.** Compared with the CIP-implied forward in §4. No requote has been obtained. |
| `PREM_PUT` | **0.0224** USD per EUR | The scenario | Undated | **Kept as an assumption.** Retail-accessible FX option quotes are unreliable, so the scenario premium stays and is flagged as such. |
| `PREM_CALL` | **0.0376** USD per EUR | The scenario | Undated | **Kept as an assumption**, same reason. |
| `FC_AMT` | 3,000,000 EUR | Scenario invoice | n/a | None. |
| `T_DAYS` | 273 days | Scenario invoice | n/a | None. |
| `BASIS` | 360 | Convention | n/a | ACT/360 for both rates, consistent with the CIP formula in the Stage 4 instructions. |

**Cross-checks of the sourced values**

- **Spot:** Two market reports from 2026-11-16 are consistent with the ECB fix: [Mitrade](https://www.mitrade.com/insights/news/live-news/article-1-2257258-20261116) ("trades around 1.1013", low 1.1002) and [TradingNews](https://www.tradingnews.com/news/eur-usd-tests-1-10-at-19-month-low-of-1-1009-reclaiming-1-1100-opens-1-1250-usd-target) (1.1018 on Monday morning, low 1.1009). Both are within 0.0004 of 1.1014.
- **Euribor 6-month:** The [global-rates 2026 history](https://www.global-rates.com/en/interest-rates/euribor/historical/2026/?id=3) lists 3.008% as the latest November value for the 6-month rate, which agrees with the Bank of Finland figure. That page gives only a monthly summary, so it does not confirm the date.
- **Treasury yields and Euribor 12-month:** Single-source for 2026-11-16. A second source (the Treasury daily par yield curve page and YCharts) could not be retrieved in this session. For 2026-11-10 the H.15 values matched YCharts (4.38% and 4.56%) when I checked at Stage 2.
- **Timing:** The ECB rate is fixed at 14:15 CET, Treasury yields are end-of-day and Euribor is a morning fixing, so the three are not simultaneous. This is a limitation of free sources.

## 3. Rate choices and rationale

- **`R_USD`: US Treasury 6-month and 1-year yields, interpolated to 273 days.** They are the standard risk-free dollar curve, published daily by the Federal Reserve, and interpolating to 273 days matches the tenor of the forward, which is what a parity check needs.
- **`R_FC`: Euribor 6-month and 12-month, interpolated to 273 days.** Euribor is the standard euro money-market reference, free to read and published on the same day-count basis (ACT/360) as the formula.
- **Why 273 days and not the stage's "1-year" rates:** The forward and the receivable are both 273 days, so a 1-year rate would compare a 273-day forward with a 365-day rate. The choice makes little difference to the conclusion (table below).
- **A known mismatch:** The USD rate is a government yield and the EUR rate is an unsecured bank reference rate. Part of any parity gap could be the cross-currency basis and credit spread. That cannot explain a gap this large (see §4).

| Rate choice | USD rate, ACT/360 | EUR rate | CIP forward | Gap to 1.1756 |
|---|---|---|---|---|
| **273-day interpolated (used)** | 4.458% | 3.178% | **1.1118** | **0.0638 (USD 191,282)** |
| 1-year Treasury and 12-month Euribor | 4.557% | 3.350% | 1.1112 | 0.0644 (USD 193,113) |
| 6-month Treasury and 6-month Euribor | 4.359% | 3.008% | 1.1124 | 0.0632 (USD 189,492) |

The gap is USD 189,000 to 193,000 under every rate choice, so the conclusion does not depend on the rate choice.

## 4. The scenario forward versus the CIP-implied forward

`F_implied = S0_in × (1 + R_USD × T_DAYS / 360) / (1 + R_FC × T_DAYS / 360) = 1.1014 × 1.033822 / 1.024115 = 1.1118`

| Measure | Value |
|---|---|
| Forward quote `F0_in` | 1.1756 |
| CIP-implied forward | 1.1118 |
| **Gap as a rate** | **+0.0638 USD per EUR**, the quote is 5.73% above the CIP forward |
| Forward premium over spot | quote +6.74%, CIP +0.95% |
| Forward proceeds | USD 3,526,800 |
| Money-market proceeds (the CIP outcome) | USD 3,335,518 |
| **Gap in USD on the receivable** | **USD 191,282** (5.42% of forward proceeds) |
| Spec §7 tolerance (1% of forward proceeds) | USD 35,268, **exceeded** |

**What the gap means.**

- **I kept the quote.** F0_in stays 1.1756, as the instructions require. The quote is not a rate I can trade at, so I am reporting the gap, not replacing the number.
- **Day count and rate choice do not explain it.** Moving to ACT/365 shifts the gap by about 0.01 percentage points, and the three rate choices in §3 move it by less than USD 4,000.
- **The rates would have to be implausible.** To justify 1.1756 at this spot with the EUR rate at 3.18%, the USD rate would have to be about 12.3%. With the USD rate at 4.46%, the EUR rate would have to be about −4.1%.
- **The scenario quotes look stale together.** Using put-call parity on the two premiums, the at-the-money spot implied by the forward and the premiums is about 1.1599, and the spot implied by the forward and the rates is about 1.1646. Both are about 5% to 6% above today's 1.1014. The forward and both premiums are undated and may have been struck at an earlier, higher spot.
- **Recommendation for Stage 5:** I would ask the bank (my instructor) for a requote of the 273-day forward on the same date as the spot, and ideally of the premiums at the new strike. If the requote comes back near 1.1118, the forward and the money-market hedge are interchangeable.

## 5. Population and re-check results

**Method.** I entered the live values into the named-range input cells only (`S0_in`, `R_USD`, `R_FC`, and the quote cells behind them `Q_USD_6M`, `Q_USD_1Y`, `Q_EUR_6M`, `Q_EUR_12M`). The strikes follow the spot by formula. `F0_in`, both premiums, `FC_AMT`, `T_DAYS` and `BASIS` were not changed.

**Workbook results at the live inputs** (all figures recomputed in Python independently of Excel and matching the workbook to the cent)

| Item | USD |
|---|---|
| Unhedged proceeds at spot (`S_T = S0_in`) | 3,304,200 |
| Forward proceeds | 3,526,800 |
| Money-market: EUR borrowed | EUR 2,929,358.52 |
| Money-market: USD received at spot | 3,226,395.47 |
| Money-market proceeds | 3,335,517.55 |
| Put premium paid today / at settlement | 67,200 / 69,472.82 |
| Put proceeds at `S_T = K_PUT` (the floor) | 3,234,727.18 |
| Call net payoff below the strike (premium only) | −116,615.08 |
| Put and forward cross at `S_T` | 1.1988 (above the grid top of 1.1565) |

- **Parity:** the flag shows FAIL, with a gap of −USD 191,282 (−5.42%).
- **Sensitivity:** The grid recalculates around the new spot, from 1.0463 to 1.1565 in 1% steps. The forward is the best strategy in all 11 rows, because the quoted forward (1.1756) is above every `S_T` in the grid. That ranking depends on the stale quote.
- **Checks tab:** 25 checks, 24 PASS and 1 FAIL (3c, the parity threshold). The FAIL is reported here as it stands.
- **What a requote would change:** If the forward were requoted at the CIP rate (1.1118), forward proceeds would equal the money-market proceeds of USD 3,335,518. The forward would win in the −5% to 0% rows, and no hedge would win in the +1% to +5% rows. This is for Stage 5 context only and is not an input.

## 6. Structural fixes (documented, not penalized)

Populating through the input cells alone gave **0 formula errors** and **10 FAIL results**. Nine were not model errors but stale typed numbers, and one was the real parity gap. The workbook was committed in this populated state first (commit 1). Each fix then went in as its own commit with its rebuilt workbook.

| Commit | Defect found | Fix |
|---|---|---|
| 1 | Populate through input cells only. Result: 0 errors, 10 FAIL (2a, 2b, 2c, 3a, 4a, 4b, 5a, 6, 7b were typed Stage 3 figures that went stale; 3c is the real gap). The date "2026-11-10" was still typed in eleven places. | None yet. This is the as-found state. |
| 2 | The Checks tab's expected figures were typed from the spec for the old inputs, so any data refresh made nine checks FAIL for the wrong reason. | The expected column is now closed-form formulas written separately from the calculation tabs (helpers in `Checks!K6:K14`), so it follows the inputs. Tolerances tightened from USD 1 to USD 0.01. Result: 1 FAIL (3c). |
| 3 | A mutation test found a coverage gap: changing the sign in the put's headline cell (`OUT_PUT`) was not caught by any check. | Added checks 4d and 5c (headline put and call outputs against closed form) and 4e and 5d (Options-tab columns against the Sensitivity columns). The same mutation now fails 4d. |
| 4 | Provenance was typed in many places (dates in the Inputs "As of" column, the Cover and Notes), and the Inputs source notes pointed to the wrong rows ("rows 23-31", the quotes are in rows 24-34). | Added `OBS_DATE` and `RETRIEVED_AT` input cells. Every as-of label, the Cover provenance block, the Notes lines and the known-issue line now read from them. Corrected the row references. Flags now say which inputs are live data and which are scenario quotes kept as given. |

**Tests after the fixes** (on copies, one change at a time, all recalculating with no errors):

- Setting `F0_in` to the CIP forward makes the parity flag PASS and all 25 checks PASS. This confirms the forward is the only input that does not tie.
- Loading the 2026-11-10 inputs, `S0_in` = 1.20, `R_FC` = 6% and `T_DAYS` = 180 leaves the closed-form checks in step with the model. Only the parity check and the rate-derivation checks (9a, 9b) fail when the typed rates no longer match the quotes, which is the intended behavior.
- Breaking the money-market step 3 formula fails 2c and 3b. Typing a number over the forward formula fails check 1.

## 7. FX Hedging Lab cross-check

**Status: performed for the forward, money-market, parity, put and sensitivity-grid outputs.** On 2026-11-18 I entered the inputs below in the [FX Hedging Lab](https://adamwstauffer.github.io/ai-lms/fxlab.html) and compared its hedge results with the workbook and with an independent Python recomputation.

| Lab input | Value |
|---|---|
| Foreign-currency receivable | EUR 3,000,000 |
| Spot | 1.1014 |
| Forward | 1.1756 |
| USD rate | 4.46% |
| EUR rate | 3.18% |
| Days | 273 |
| Put and call strike | 1.1014 |
| Put premium / call premium | 0.0224 / 0.0376 |

| Output | Workbook | Lab | Agree? |
|---|---|---|---|
| Forward proceeds | USD 3,526,800.00 | $3,526,800 | Yes |
| Money-market step 1: EUR borrowed | EUR 2,929,358.52 | 2,929,359 EUR | Yes (the lab rounds to the euro) |
| Money-market step 2: USD received at spot | USD 3,226,395.47 | $3,226,395 | Yes |
| Money-market step 3: USD proceeds | USD 3,335,517.55 | $3,335,518 | Yes |
| Implied forward | 1.1118 | 1.1118 | Yes |
| Parity gap | −USD 191,282 | −$191,282 | Yes |
| Put floor (S_T at or below the strike, −5% to 0%) | USD 3,234,727.18 | $3,237,000 | No: Lab is USD 2,272.82 higher (premium convention, below) |
| Put, +1% (S_T 1.1124) | USD 3,267,769.18 | $3,270,042 | No: same USD 2,272.82 |
| Put, +3% (S_T 1.1344) | USD 3,333,853.18 | $3,336,126 | No: same USD 2,272.82 |
| Put, +5% (S_T 1.1565) | USD 3,399,937.18 | $3,402,210 | No: same USD 2,272.82 |
| Unhedged, forward and money-market columns, all 11 rows | e.g. −5%: 3,138,990 / 3,526,800 / 3,335,518 | identical in every row | Yes |
| Best strategy, all 11 rows | Forward | Forward | Yes |

**Resolution of the parity flag.** The lab prints "Parity gap of $-191,282 — in a real model this means a formula error (wrong rate basis, inverted ratio, or mis-sized loan), not an arbitrage." That is the lab's general message. Here the lab, the workbook and the independent recomputation agree on every figure to the dollar, so the gap is not a formula error in either tool. The three usual suspects are ruled out: the rate basis is ACT/360 in both, the ratio is not inverted (the implied forward is the same in both), and the loan is sized as `FC_AMT / (1 + R_FC × T/360)` in both. Setting `F0_in` to the implied forward makes the workbook's parity check pass, so the gap comes from the quoted forward, not from the model. I read it as a stale quote, not an arbitrage, and I am reporting it and asking for a requote instead of changing any input.

**Put and grid: what differs and why.** The Lab's put column is USD 3,237,000 at or below the strike, which is exactly `FC_AMT × K_PUT − FC_AMT × PREM_PUT` = 3,304,200 (3,000,000 × 1.1014), less the premium at face value of USD 67,200 = 3,237,000. The workbook deducts the premium at its day-273 value, USD 67,200 × (1 + 4.46% × 273/360) = USD 69,472.82, giving 3,234,727.18. Every put row in the grid differs by the same USD 2,272.82, which is the interest on the premium over 273 days, so the two tools use one payoff and differ only in the timing of the premium. The workbook's convention follows spec §4: the premium is paid today and the euros arrive in 273 days, so the two amounts must be put on the same date before they are compared. The Lab treats a dollar paid today as a dollar received on day 273. Neither is a formula error, and I have kept the workbook's convention.

**What the difference changes.** Nothing in the ranking of the best strategy: the forward is best in all eleven rows in both tools. It does move one comparison below the forward. At +3% the Lab's put (USD 3,336,126) is above money market (USD 3,335,518), while the workbook's put (USD 3,333,853) is below it. The put also breaks even with the money-market hedge about USD 2,273 sooner under the Lab's convention. Stage 5 should state which convention it uses.

**The Lab's chart.** It plots the forward and money-market lines as flat, the unhedged line rising at a constant slope, and the put as flat from the left until about S_T = 1.1014 (the strike) and then rising with the same slope as the unhedged line. That is the shape the workbook's `Sensitivity` chart shows.

## 8. Assumptions and limitations

- The forward quote and both option premiums are the scenario's, not live. The premiums are kept as an explicit assumption.
- The sources are free and not simultaneous (§2). A terminal (Bloomberg or Refinitiv) would provide a forward quote and option quotes at one timestamp.
- Treasury yields and Euribor 12-month are single-sourced for 2026-11-16.
- The workbook is static and full-notional, with no transaction costs, bid-ask spreads or counterparty risk (spec §4).
- The workbook was recalculated in LibreOffice, not Excel. Excel will recalculate when it opens the file.
