# Stage 5 validation — online-degrees (scenario 16)

**Date:** 2026-11-26
**Workbook under test:** `capabilities/fx-hedging/model.xlsx` (Stage 4 final, 25 checks, 24 PASS and 1 FAIL: 3c, the parity threshold)
**Documents given to the fresh LLM:** `capabilities/fx-hedging/spec.md` (v0.5) and `data/2026-11-18-online-degrees-market-data-memo.md` (the version that includes the FX Lab put and grid comparison)
**Fresh LLM:** a new Claude conversation, no history.
**Prompt, full text:** see Appendix B. Raw LLM output: Appendix A.
**Author of the hand verification and the diagnoses:** Curran Hirata, with Claude's help drafting.

## Summary

The fresh LLM reproduced every base-case figure in the memo and the workbook, and every grid row it printed, to the dollar. It made **two arithmetic errors** in the put break-evens, and it **did not give one recommendation**. All three trace to the same gap: my spec defines the strategies and checks but not the break-even quantities or the firm's risk posture. I found **no workbook error**.

| # | Finding | Diagnosis | Fed into |
|---|---|---|---|
| 1 | Put versus no-hedge break-even: LLM says 1.0814 (−1.8%); correct is 1.0782 (−2.1%) | LLM error; spec gap (break-even not defined) | Retrospective 1 |
| 2 | Put versus money-market crossover: LLM says about 1.1365 (+3.2%); correct is 1.1350 (+3.05%) | LLM error; spec gap (same) | Retrospective 1 |
| 3 | Call shown only at the base case | LLM omission; spec gap (call values not in §7 at several `S_T`) | Retrospective 4 |
| 4 | Recommendation is conditional ("get a requote first"), not one strategy | Spec gap: no risk posture or decision rule | Retrospective 3 |
| 5 | Everything else matches | None | — |

## 1. Test protocol

- [x] Fresh LLM conversation, no history.
- [x] Exactly two documents provided: the spec and the market-data memo. I attached them as files copied from the raw files on GitHub.
- [x] No coaching, no correction mid-run, no workbook results pasted.
- [x] Prompt logged in `prompt-log.md` and reproduced in Appendix B.
- [x] Raw output saved in Appendix A, with the formatting of the original flattened (tables use `|` separators; the wording is verbatim).

**A limit on this test.** The memo contains the workbook's base-case results (memo §5 and §7: forward, money-market steps, put floor, call, the 1.1988 put-forward crossover, the gap, and the Lab's grid rows at +1% and +3%). Agreement on those figures is weak evidence that the spec stands alone, because the LLM could read them instead of computing them. The two errors appeared on exactly the quantities the memo does **not** print (the break-evens against no hedge and against money-market). That is where the real test of the spec happened.

## 2. Comparison: LLM versus workbook

All amounts are USD proceeds at settlement. Workbook figures are from `Sensitivity` and `Options`; the LLM's are from Appendix A.

| Strategy | `S_T` | Workbook | LLM | Match |
|---|---|---|---|---|
| No hedge | 1.0463 (−5%) | 3,138,990 | 3,138,990 | Yes |
| No hedge | 1.1014 (0%) | 3,304,200 | 3,304,200 | Yes |
| No hedge | 1.1565 (+5%) | 3,469,410 | 3,469,410 | Yes |
| Forward (1.1756) | any | 3,526,800 | 3,526,800 | Yes |
| Money-market | any | 3,335,518 | 3,335,518 | Yes |
| Put | 1.0463 (−5%) | 3,234,727 | 3,234,727 | Yes |
| Put | 1.1014 (0%) | 3,234,727 | 3,234,727 | Yes |
| Put | 1.1565 (+5%) | 3,399,937 | 3,399,937 | Yes |
| Call (reference) | 1.1014 (0%) | −116,615 | −116,615 | Yes |
| Call (reference) | 1.0463 (−5%) | −116,615 | not given | Omitted |
| Call (reference) | 1.1565 (+5%) | +48,595 | not given | Omitted |

Other quantities the LLM stated:

| Quantity | Workbook / my arithmetic | LLM | Match |
|---|---|---|---|
| CIP-implied forward | 1.1118 | 1.1118 | Yes |
| Gap, rate and USD | 0.0638; USD 191,282 (5.42% of forward) | same | Yes |
| Parity tolerance (1% of forward) | USD 35,268 | USD 35,268 | Yes |
| Put floor below the certain hedges | USD 100,790 | "about USD 100,800" | Yes (rounded) |
| Put beats no hedge at −5% | USD 95,737 | USD 95,737 | Yes |
| Premium at day 273 | USD 69,473 | USD 69,473 | Yes |
| Put overtakes forward | `S_T` 1.1988 | about 1.1988 | Yes |
| Put versus no-hedge break-even | **1.0782 (−2.1%)** | 1.0814 (−1.8%) | **No** |
| Put overtakes money-market | **1.1350 (+3.05%)** | about 1.1365 (about +3.2%) | **No** |
| Winner in every grid row | Forward | Forward | Yes |

The LLM printed six of the eleven grid rows (−5%, −3%, 0%, +1%, +3%, +5%) and says it computed all eleven. Every row it printed matches `Sensitivity`.

## 3. Diagnosis of the discrepancies

**Finding 1 — put versus no-hedge break-even.**
The put beats no hedge while `K_PUT − PUT_PREM_FV / FC_AMT > S_T`.
`1.1014 − 69,472.82 / 3,000,000 = 1.1014 − 0.023158 = 1.0782`, which is −2.10% from spot.
Check against the grid: at −2% (`S_T` 1.0794) no hedge gives 3,238,116 and the put gives 3,234,727, so the put is already behind there. A break-even at −1.8% (1.0814) would put it ahead at −2%, which the grid contradicts.
**Diagnosis: LLM error.** I can't tell how it reached 1.0814. The workbook does not compute this figure and the spec does not define it, so the spec contributed by leaving the LLM to derive it.

**Finding 2 — put versus money-market crossover.**
The put beats money-market when `FC_AMT × S_T − PUT_PREM_FV > MM`, so `S_T > (3,335,517.55 + 69,472.82) / 3,000,000 = 1.1350`, which is +3.05%.
Check against the grid: at +3% (1.1344) the put is 3,333,853, below money-market's 3,335,518; at +4% it is above. The LLM's 1.1365 falls in the same grid interval, so its table is right, but the number it quotes is not.
**Diagnosis: LLM error, same spec gap as Finding 1.** The only crossover the spec defines is the put-forward one (`F0_in + PUT_PREM_FV / FC_AMT`, 1.1988), and the LLM got that one right.

**Finding 3 — call at one `S_T` only.**
Workbook: `CALL = FC_AMT × MAX(S_T − K_CALL, 0) − 116,615.08`, which is −116,615 at −5% and 0%, and +48,595 at +5%. The LLM gave only the base-case figure and called the call "not a hedge for a receivable", which follows spec §6.
**Diagnosis: LLM omission, with a spec contribution.** Spec §7 check 5 states the call only as "−USD 116,572 for every `S_T ≤ K_CALL`" and does not give a value above the strike.

**Finding 4 — no single recommendation.**
The LLM recommended getting a requote and then choosing between the forward and money-market, with a put only if the CFO wants upside. It couldn't choose, because neither document states the firm's risk posture or how much it would pay for protection.
**Diagnosis: spec gap.** See Retrospective 3.

**No workbook errors.** Every workbook figure above agrees with my hand calculation (Section 4) and with the LLM except where the LLM was wrong.

**A near-miss, not a discrepancy.** Spec §7 still lists the 2026-11-10 design-time figures (put floor USD 3,294,753, call −USD 116,572, money-market USD 3,396,350, grid 1.0653–1.1775), while the memo has the 2026-11-16 figures. The LLM used the memo's. See Retrospective 2.

## 4. Hand verification (calculator, named-range notation, no Excel)

Inputs: `FC_AMT` = 3,000,000; `S0_in` = 1.1014; `F0_in` = 1.1756; `R_USD` = 4.46%; `R_FC` = 3.18%; `T_DAYS` = 273; `BASIS` = 360; `K_PUT` = 1.1014; `PREM_PUT` = 0.0224.
I did these calculations on a calculator, not in Excel.

| # | Outcome | Arithmetic | Result | Workbook |
|---|---|---|---|---|
| 1 | Forward proceeds | `FC_AMT × F0_in` = 3,000,000 × 1.1756 | USD 3,526,800 | 3,526,800.00 |
| 2a | MM step 1: euros borrowed | Foreign growth factor = 1 + 0.0318 × 273 / 360 = 1 + 0.024115 = 1.024115. `FC_AMT` / 1.024115 = 3,000,000 / 1.024115 | EUR 2,929,358.52 | 2,929,358.52 |
| 2b | MM step 2: convert at spot | `FC_BORROW × S0_in` = 2,929,358.52 × 1.1014 | USD 3,226,395.47 | 3,226,395.47 |
| 2c | MM step 3: invest in USD | USD growth factor = 1 + 0.0446 × 273 / 360 = 1.0338217. 3,226,395.47 × 1.0338217 | USD 3,335,517.65 | 3,335,517.55 (10 cents lower; see note below) |
| 3a | Put premium at day 273 | Face = 3,000,000 × 0.0224 = 67,200. Future value = 67,200 × 1.0338217 | USD 69,472.82 | 69,472.82 |
| 3b | Put outcome at `S_T` = 1.0463 (−5%) | `S_T` < `K_PUT`, so exercise. `FC_AMT × MAX(S_T, K_PUT)` = 3,000,000 × 1.1014 = 3,304,200. Less 69,472.82 | USD 3,234,727.18 | 3,234,727.18 |
| 3c | Put outcome at `S_T` = 1.1565 (+5%) | `S_T` > `K_PUT`, so let it expire. 3,000,000 × 1.15647 = 3,469,410. Less 69,472.82 | USD 3,399,937.18 | 3,399,937.18 |
| 4 | CIP-implied forward | `S0_in × 1.0338217 / 1.024115` = 1.1014 × 1.009478 | 1.11184 | 1.11184 |
| 5 | Parity gap | In USD: forward proceeds − money-market proceeds = 3,526,800 − 3,335,517.65 = 191,282.35. As a rate: 1.1756 − 1.11184 = 0.06376 per EUR | USD 191,282; 0.06376 per EUR | 191,282.45; 0.0638 |
| 6 | Put break-even versus no hedge | 1.1014 − 69,472.82 / 3,000,000 | 1.0782 | not computed |
| 7 | Put versus money-market crossover | (3,335,517.65 + 69,472.82) / 3,000,000 = 3,404,990.47 / 3,000,000 | 1.1350 | not computed |

**Rounding notes.**
- **Line 2c:** my calculator uses the rounded factor 1.0338217, which gives 3,335,517.65. The workbook carries the factor at full precision (1.033821666…) and gets 3,335,517.55. The 10-cent difference is rounding in the factor, not an error.
- **Line 5:** the dollar gap is the difference between the two proceeds, USD 191,282. Multiplying the rate gap by the receivable gives 0.06376 × 3,000,000 = 191,280, which is about USD 2 lower because the rate is rounded to five places (the unrounded rate gap is 0.0637608).

Reconciliation: lines 1 to 4 agree with the workbook to the cent, except the 10 cents on line 2c explained above, and line 5 agrees to the dollar. Lines 6 and 7 are the two quantities the LLM got wrong; my hand figures agree with the grid (Section 3).

## 5. Spec retrospective

What the LLM got wrong or had to guess, and what it says about my spec:

1. **Break-evens were left for the reader to derive, and the LLM derived two of them wrongly.** The spec defines the put-forward crossover (§6, `F0_in + PUT_PREM_FV / FC_AMT`) but not the put-versus-no-hedge break-even or the put-versus-money-market crossover. Those are the numbers that decide whether the put is worth buying, and the LLM got both wrong (1.0814 for 1.0782, 1.1365 for 1.1350). **v2:** name all three as outputs on the `Options` tab (`BE_PUT_UNH = K_PUT − PUT_PREM_FV / FC_AMT`, `BE_PUT_MM = (MM + PUT_PREM_FV) / FC_AMT`, `BE_PUT_FWD`), put the formulas in §6, and put their values in the §7 check figures.
2. **The spec carries two sets of numbers.** §7 still lists the 2026-11-10 figures (put floor 3,294,753, call −116,572, money-market 3,396,350, grid 1.0653–1.1775). The memo has the live ones. The LLM used the memo's, but nothing in §7 tells a fresh reader which set wins, and a less careful reader could have copied the stale floor. **v2:** drop result figures from the spec body, keep the formulas, and put one table of design-time figures and one of live figures, labeled by date, in an appendix.
3. **No risk posture and no decision rule.** The LLM could not pick a strategy because neither document says what the firm values (certainty versus upside), how much protection it will pay for, or what to do if the forward quote can't be trusted. The spec has the Stage 1 stress figures but no appetite. **v2:** add a "Decision criteria" section: the firm's stated posture, a maximum acceptable cost of protection as a share of proceeds, and the rule for choosing between forward and money-market when they differ.
4. **The call was under-specified above the strike.** §7 check 5 gives only the value below the strike, and the LLM reported only that. **v2:** give the call at three `S_T` points, as for the put.
5. **What worked.** The premium convention (§4: future-valued at `R_USD`) was followed exactly, and the LLM spotted the difference from the FX Lab's face-value convention because the memo records it. The ACT/360 basis, the interpolation and the money-market steps were reproduced without a hint, so the inputs table and §4 were enough for those.
6. **My test design was too generous.** The memo hands over the answers it should only support (Section 1). **v2 of the process:** keep results out of the market-data memo, which should hold inputs and sources only, and keep results in the workbook and this validation document. I wouldn't change the memo for Stage 4, where the brief asked for the comparison, but I would split it.

## Appendix A — raw LLM output

The text below is the LLM's reply, unedited except that its tables are shown with `|` separators because the original formatting did not survive copying.

```text
Recommendation: don't commit to a hedge on the scenario forward yet. Get a fresh 273-day forward quote at the same time as the spot. If the quote comes back near 1.1118, hedge with the forward or the money-market hedge, which will be interchangeable. If the quote stays at 1.1756, take the forward.

The 1.1756 forward doesn't tie to the market. I recomputed every figure from your inputs (spot 1.1014, USD 4.46%, EUR 3.18%, 273 days, ACT/360) and they match your memo.

Hedge outcomes at the base case (USD)

Strategy | Proceeds | Notes
No hedge at spot (1.1014) | 3,304,200 | Fully exposed to the euro
Forward at 1.1756 | 3,526,800 | Locked in
Money-market | 3,335,518 | Borrow EUR 2,929,359, convert to USD 3,226,395, invest to day 273
Put (at-the-money strike 1.1014) | 3,234,727 floor | Premium USD 67,200 today, USD 69,473 at day 273
Call (reference only) | −116,615 | Not a hedge for a receivable

Sensitivity grid (USD)

Move in S_T | S_T | Unhedged | Forward | Money-market | Put | Best
−5% | 1.0463 | 3,138,990 | 3,526,800 | 3,335,518 | 3,234,727 | Forward
−3% | 1.0684 | 3,205,074 | 3,526,800 | 3,335,518 | 3,234,727 | Forward
0% | 1.1014 | 3,304,200 | 3,526,800 | 3,335,518 | 3,234,727 | Forward
+1% | 1.1124 | 3,337,242 | 3,526,800 | 3,335,518 | 3,267,769 | Forward
+3% | 1.1344 | 3,403,326 | 3,526,800 | 3,335,518 | 3,333,853 | Forward
+5% | 1.1565 | 3,469,410 | 3,526,800 | 3,335,518 | 3,399,937 | Forward

I computed all 11 rows; the forward is best in every one.

Put versus unhedged: The put beats no hedge by USD 95,737 at −5%, and is USD 69,473 behind (the future-valued premium) from the strike upward. It breaks even against no hedge at about S_T = 1.0814, roughly −1.8%.
Put versus forward: The put only overtakes the forward above S_T ≈ 1.1988, well above the top of the grid.
Put versus money-market: The put beats money-market only at +4% and +5%.

Why the forward wins is not a reliable result

The forward is out of line: The covered-interest-parity forward is 1.1118, and the quote of 1.1756 is 0.0638 above it. That is USD 191,282 on the receivable (5.42% of forward proceeds), well outside the 1% tolerance of USD 35,268.
The cause is the quote, not the model: Your memo rules out day count, rate choice and formula errors. The quote is undated, and the premiums look stale for the same reason.
A real quote changes the answer: At a forward of 1.1118, forward proceeds equal money-market proceeds (USD 3,335,518). The forward then wins only in the −5% to 0% rows, and no hedge wins in the +1% to +5% rows.
That would put the put in play: If the requote returns near 1.1118, the put's floor of USD 3,234,727 is about USD 100,800 below the certain hedges. You would pay for upside you only use if the euro rises more than about 3.2% (S_T above ~1.1365, where the put overtakes the certain hedges).

What to do

Ask for a same-day 273-day forward quote and re-quoted at-the-money option premiums.
If the forward is near 1.1118, use the forward or money-market hedge. They are equivalent, so choose on borrowing capacity and cash-deposit needs.
If the bank honors 1.1756, take the forward. It locks in USD 191,282 more than money-market.
Use the put only if the CFO wants upside participation and accepts a USD 69,473 cost with a floor about USD 100,000 below the certain hedges.

State the premium convention in the memo. I used the day-273 value (USD 69,473). The FX Lab uses face value (USD 67,200), which lifts every put figure by USD 2,273 and moves the +3% put above money-market.

Limitations: transaction costs, spreads and counterparty risk are excluded. Treasury yields and the 12-month Euribor are single-sourced.
```

## Appendix B — the prompt sent to the fresh LLM

```text
Hello, I need you to compute all hedge outcomes and recommend a strategy from these two documents. Thank you.
```

The two attached files were the spec and the market-data memo, copied from the raw files on GitHub.
