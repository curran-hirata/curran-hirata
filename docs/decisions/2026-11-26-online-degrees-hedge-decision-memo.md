# Hedge decision memo — EUR 3,000,000 receivable (online-degrees)

**To:** CFO / Director of Treasury
**From:** Curran Hirata
**Date:** 2026-11-26
**Market data:** ECB spot 1.1014 (2026-11-16); USD 4.46% and EUR 3.18%, 273 days, ACT/360. Sources and timestamps are in `data/2026-11-18-online-degrees-market-data-memo.md`. Calculations are in `capabilities/fx-hedging/model.xlsx` and were checked by hand and by an independent run in `analysis/2026-11-26-online-degrees-validation-analysis.md`.

## Recommendation in one paragraph

**Hedge with a forward, and execute it only on a fresh 273-day quote at or above 1.1118. If the bank quotes below 1.1118, use the money-market hedge instead.** Do not buy the put. Until a firm quote is in hand, budget USD 3,335,518, not the USD 3,526,800 the indicative forward implies.

## A. Exposure summary

We will receive EUR 3,000,000 from the Dutch partner in 273 days. At today's spot of 1.1014 that is USD 3,304,200. Every cent the euro moves is worth USD 30,000.

## B. Hedge outcomes

| Strategy | USD at settlement | What it means for the decision |
|---|---|---|
| No hedge | 3,304,200 at today's spot; 3,138,990 if the euro falls 5%; 3,469,410 if it rises 5% | The only strategy that gains from a rising euro, and the only one with no floor. A 5% fall costs USD 165,210 against today's spot (the Stage 1 brief's USD 176,000 is the same 5% measured against the forward proceeds). |
| Forward at the quoted 1.1756 | 3,526,800, fixed | Beats every other strategy in all eleven scenarios, but only because 1.1756 is far above what the market supports (Section D). |
| Money-market hedge | 3,335,518, fixed | Replicates a forward from rates we can verify. It is the benchmark: any forward at or above 1.1118 is at least as good. |
| Put, strike 1.1014 | Floor 3,234,727; rises one-for-one above the strike | Costs USD 69,473 (premium USD 67,200 paid today, carried to day 273). Its floor is USD 100,790 below the money-market hedge. |
| Call | Not a hedge for a receivable | Priced for reference only; ignored below. |

The key point is the gap between the money-market result and the unhedged result at today's spot: USD 31,318. That is the interest-rate differential, the true price of certainty at market rates. It is not a cost. The put costs USD 69,473, more than twice that.

## C. Sensitivity interpretation

**If the euro falls**, both certain hedges hold their value and the put holds its floor. The put saves USD 95,737 against no hedge at −5%, but it still lands USD 100,790 below the money-market hedge, so the put is a worse way to buy protection than the forward even in the scenario it was bought for.

**If the euro rises**, the certain hedges leave money on the table and the put keeps the upside minus the premium. The put overtakes the money-market hedge only above `S_T` 1.1350 (+3.05%). It never beats no hedge: above the strike it trails by the premium, USD 69,473. At +5% the unhedged result is 3,469,410, the put 3,399,937 and the money-market hedge 3,335,518.

**Break-evens that matter:**
- The put beats no hedge only below `S_T` 1.0782 (−2.1%).
- The put overtakes the money-market hedge above 1.1350 (+3.05%).
- A forward at the market-supported 1.1118 would be overtaken by no hedge above 1.1118, which is only +0.95% from today's spot.
- The put overtakes the quoted forward at 1.1988, which is +8.8% from today's spot and above the top of the grid.

In short: the forward and money-market give certainty, the put gives a floor that costs more than the protection is worth at these prices, and no hedge gives full exposure both ways.

## D. Recommendation

**The quoted forward and the CIP-implied forward do not agree.**

| | Rate (USD per EUR) | USD on EUR 3,000,000 |
|---|---|---|
| Bank's indicative forward | 1.1756 | 3,526,800 |
| CIP-implied forward (spot 1.1014, USD 4.46%, EUR 3.18%) | 1.1118 | 3,335,518 |
| **Gap** | **0.0638** | **191,282 (5.42% of forward proceeds)** |

The gap is five times the 1% tolerance (USD 35,268) we set as the test of a sound quote. The EUR rate is 1.28 points below the USD rate, which supports a forward about 0.95% above spot. The quote is 6.7% above spot.

**What I think explains it.** The quote is an undated scenario figure, and the two option premiums fail the same test. Taking the quote and the live rates together, the spot the forward implies is about 1.1646, a euro 5.7% stronger than the ECB's 1.1014. The euro fell from 1.1214 on 2026-11-10 to 1.1014 on 2026-11-16, so the most likely story is a quote struck at a higher spot. This is inference, not proof. I ruled out the day count, the rate choices and the formulas (all of them move the gap by less than USD 2,000 or not at all), and an independent tool and an independent model run both reproduced the gap to the dollar.

**Does it change the recommendation?** It changes the number and the way we execute, not the strategy.
- If the bank honors 1.1756 on a firm quote, take it. It locks USD 191,282 more than the money-market hedge.
- A quote 5.4% off covered parity is not one a bank would normally honor, so I would not budget on it.
- On a fresh same-day quote at or above 1.1118, take the forward. Below 1.1118, use the money-market hedge, which needs borrowing capacity in euros.
- Either way the answer is a certain hedge, not the put.

**Stage 1 hypothesis, checked.** The brief expected the forward and the money-market hedge to lock in nearly the same dollars, and said the hypothesis would be wrong if they differed by more than about 1% (USD 35,300). They differ by 5.42% (USD 191,282), so on the quoted forward that part of the hypothesis is rejected, for the reason above. The brief's put break-even of about 1.198 against the forward holds: the model gives 1.1988.

**Next step:** ask the bank for a same-day 273-day forward and re-quoted at-the-money option premiums.

## E. Executive justification

- **Cash-flow stability and budget certainty.** A certain hedge fixes the dollars the program can spend, to the dollar, regardless of the euro. The put leaves the budget between USD 3,234,727 and unlimited.
- **Premium cost.** The put costs USD 69,473, 2.1% of unhedged proceeds. The forward and the money-market hedge have no premium; their cost is the interest differential (USD 31,318).
- **Optionality.** The put is the only way to share in a euro rally above 1.1350 while protected below. At these prices that option is expensive, and the put only wins if the euro rises more than 3.05% by settlement. I assume the board does not hold that view; if it does, it should say so, and the put would be the choice to revisit on a re-quoted premium.
- **Liquidity.** The forward needs no cash today. The money-market hedge needs euro borrowing now and a USD deposit, which ties up balance-sheet capacity. The put needs USD 67,200 today.
- **Risk posture.** The Stage 1 brief frames the problem as protecting a budget: the euro amount is fixed, the dollars are not, and a fall in the euro costs the program money it has already planned to spend. It values certainty ("the budget is safe") over keeping upside, which the brief names as the price of a forward. This memo follows that posture.

**Limits of this analysis.** Transaction costs, spreads, taxes and counterparty credit risk are excluded. Treasury yields and the 12-month Euribor are single-sourced. The put figures use the premium future-valued to day 273; at face value every put figure is USD 2,273 higher, and the ranking of strategies is unchanged.

## Decision record

| Field | Entry |
|---|---|
| **What was decided** | Hedge the EUR 3,000,000 receivable with a forward, executed on a fresh 273-day quote at or above 1.1118; use the money-market hedge if the quote is below 1.1118; do not buy the put. |
| **Why** | A certain hedge protects the budget at no premium. The put costs USD 69,473 and its floor is USD 100,790 below the money-market hedge. The indicative forward (1.1756) is USD 191,282 above the covered-parity forward and cannot be budgeted on. |
| **Who decided it** | Recommended by Curran Hirata. The decision belongs to the CFO / Director of Treasury. |
| **What would reverse it** | A firm forward quote below 1.1118 (switch to the money-market hedge). A board view that the euro will rise more than 3.05% by settlement, which would justify re-pricing the put on a fresh premium. A change in the firm's risk posture toward keeping upside. |
