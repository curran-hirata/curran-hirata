---
type: brief
title: "Online-Degrees Hedging"
engagement: fx-hedging
capability: fx-hedging
scenario: online-degrees
date: 2026-10-29
status: committed
hypothesis: "I expect the forward and money-market hedge to lock in nearly the same dollar amount, and the put to beat them only if the euro rises enough to cover its premium, because interest-rate differences are already built into the forward price."
llm_used: "Claude"
---

# Online-Degrees Hedging

**Created by:** Curran Hirata  
**Updated by:** Curran Hirata & Claude  
**Date Created:** 10-27-2026  
**Date Updated:** 10-29-2026  
**Version:** 1.5  
**LLM Used:** Claude

---

## Executive Summary

A U.S. online-degree provider has invoiced its Dutch university partner EUR 3,000,000, payable in 273 days (about nine months). The euro amount is fixed, but the dollar amount is not, because it depends on the exchange rate on the day payment arrives. At today's indicative forward rate of 1.1756 dollars per euro, the receivable is worth $3,526,800. If the euro weakens by 5% before payment, the company receives about $176,000 less than budgeted, and a 9% fall costs about $317,000. Locking in a rate today guarantees the dollar amount even if the euro weakens.

---

## Background & Objectives

The objective is to compare three hedge families before recommending one. The first is a forward contract: the company agrees today to sell the euros at 1.1756 on the payment date. The dollar amount becomes certain and the budget is safe, but any gain is forfeited if the euro strengthens, and the bank on the other side may require cash deposits.

The second is a money-market hedge: the company borrows euros now, converts them to dollars at today's rate, invests the dollars, and uses the incoming euro payment to repay the borrowing. It creates the same certainty as a forward using ordinary interest rates, but it uses up borrowing capacity and requires both euro and dollar interest rates.

The third is a put option, which gives the company the right, but not the obligation, to sell euros at today's rate. It sets a minimum (floor) on the dollars received while leaving room to benefit if the euro rises. The cost is a premium of $67,200 (the quoted $0.0224 per euro), paid up front whether or not the option is used.

---

## What I Am Assuming

I am assuming that the Dutch partner pays the EUR 3,000,000 in full 273 days from now, that the entire amount is converted into U.S. dollars using the provided forward and option rates, and that trading fees, taxes, and default risk are negligible. For the money-market hedge, I will use standard nine-month interest rates and the current market exchange rate.

---

## Hypothesis

I expect the forward and the money-market hedge to deliver almost the same dollar amount, about $3,526,800, because the forward price already reflects the gap between dollar and euro interest rates. I expect the put's $67,200 premium to make it the better choice only if the euro finishes above roughly 1.198, the forward rate plus the premium.

---

## How I Would Know I Was Wrong

I would reject my hypothesis if my completed model shows the money-market hedge differing from the forward by more than about 1% (roughly $35,300), which would suggest my assumptions are off. I would also reject it if the put's break-even rate, once real market rates and the cost of financing the premium are included, differs materially from about 1.198 (the 1.1756 forward rate plus the $0.0224 premium).

---

## Methods & Next Steps

Stage 2 designs the spreadsheet model on paper, including its inputs, formulas, and checks. Stage 3 has an AI tool build the model from that design, followed by an audit of the work. Stage 4 replaces the placeholders with sourced, timestamped market prices. Stage 5 validates the results independently and delivers the final recommendation in a decision memo.

---

## References

