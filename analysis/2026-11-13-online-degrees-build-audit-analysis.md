# Stage 3 build audit — online-degrees (scenario 16)

**Date:** 2026-11-13
**Workbook:** `capabilities/fx-hedging/model.xlsx`, built from `capabilities/fx-hedging/spec.md` v0.4
**Built with:** Claude (AI build from the spec)
**Audited by:** recalculating the file in LibreOffice and running scripts over every cell, plus the Excel steps in "Reproduce in Excel" below, which still need to be run by the author

## Summary

The build has no formula errors and no hard-coded results. Every spec §7 check figure reproduces, with one exception: the parity threshold (check 3c) **fails**, and I report it rather than force it to pass. The failure is in the scenario's forward rate, not in the model. The build also exposed four gaps in the spec itself, now fixed in v0.4.

| # | Finding | Type | Status |
|---|---|---|---|
| 1 | Parity check fails: money-market proceeds are 3.70% (USD 130,450) below the forward | Data / spec §7 | Open, reported, fix at Stage 4 |
| 2 | Forward and both option premiums look jointly stale against the 1.1214 spot | Data | Open, flagged |
| 3 | Spec Legend-Key colors contradicted the build contract | Spec defect | Fixed in spec v0.4 |
| 4 | Spec had no home for the §7 checks and did not name the grid parameters | Spec defect | Fixed in spec v0.4 |
| 5 | No hard-coded results; named ranges attached to the right cells | Confirmed | Closed |
| 6 | Workbook responds to input changes; check figures are base-case values and typed rates do not follow `T_DAYS` | Design limit | Documented, refresh at Stage 4 |

## Finding 1 — the parity check fails because of the scenario forward

**What I checked.** Spec §7 check 3: `|MM − FWD|` must be within 1% of the forward (USD 35,268), the Stage 1 falsifier. The workbook computes it on `Money-Market` (`OUT_PARITY_FLAG`) and on `Checks` (3c).

**What I found.**
- The forward is USD 3,526,800. Money-market proceeds are USD 3,396,350. The gap is **−USD 130,450 (−3.70%)**, so the flag shows FAIL.
- The parity-implied forward is 1.1321. The scenario forward is 1.1756, which is 4.83% above the 1.1214 spot. The rates imply only a 0.95% premium.
- The identity check 3b (`FC_AMT × F_IMPLIED = MM`) passes to the cent, so the money-market pipeline itself is algebraically right.

**Diagnosis, by testing the cause.**

| Test (change one input, recalculate) | Parity gap | Flag |
|---|---|---|
| Base case | −3.699% | FAIL |
| `F0_in` set to the rate-implied 1.1321 | +0.001% | PASS |
| `BASIS` set to 365 instead of 360 | −3.711% | FAIL |
| `R_FC` set to 6.00% | −5.711% | FAIL |

- Setting `F0_in` to 1.1321 makes the check pass, so the forward is the unmatched input.
- The day count is not the cause. Moving from ACT/360 to ACT/365 changes the gap by only 0.01 percentage points.
- To justify 1.1756 at this spot, the USD rate would need to be about 9.6% with the EUR rate at 3.12%, a spread of about 6.5 points. The market spread is 1.3 points.

**What I did.** Left `F0_in` at 1.1756 as the scenario and my teacher's feedback require. The workbook shows FAIL on the Cover, Money-Market and Checks tabs. Spec §2.1 note 2 and the §7 paragraph on a gap wider than 1% now describe the finding. At Stage 4, replace `F0_in` with a bank quote for 273 days taken at the same time as the spot.

**Effect on the decision.** On the scenario's forward the forward is the best strategy in every grid row except +5%, where unhedged proceeds win by USD 5,610. The money-market hedge never wins. That ranking depends on a forward the market does not support, so Stage 5 should not rely on it until the forward is re-quoted.

## Finding 2 — the scenario quotes look jointly stale

**What I checked.** Put-call parity on the two option premiums, as a second independent test of the forward. For at-the-money options, `C − P = (F − K) / (1 + R_USD × T_DAYS / BASIS)`. Added to the workbook as `Checks` rows 10a to 10d (informational).

**What I found.**

| Route | Result |
|---|---|
| Forward implied by the rates (at spot 1.1214) | 1.1321 |
| Forward implied by the premiums (strike = 1.1214) | 1.1371 |
| Scenario forward `F0_in` | 1.1756 |
| Spot implied by `F0_in` and the rates | 1.1645 |
| At-the-money spot implied by `F0_in` and the premiums | 1.1599 |

The two market-based forwards are within 0.005 of each other. The scenario forward is about 0.038 away from both. Both ways of backing out a spot from the scenario quotes land near 1.16, about 3.5% above the observed ECB spot.

**What I did.** Flagged as spec §2.1 note 3 and §7 check 9. This is inference, not proof: the premiums are also undated scenario quotes. The point is that the forward and both premiums may all have been struck at an earlier, higher spot, so setting the strikes to 1.1214 may pair them with an option they do not describe. At Stage 4, re-quote the forward and both premiums on the same date and time as the spot.

## Finding 3 — spec Legend-Key colors contradicted the build contract

**What I checked.** The spec's tab table against the Stage 3 build contract, item 4.

**What I found.** Spec v0.3 said "black = formula, green = cross-tab link". The contract says "Yellow = inputs · Blue = assumptions · Green = formulas · Gray = outputs". An AI following v0.3 would color formulas black and fail the contract.

**What I did.** Built to the contract (green fill for formulas, including cross-tab links) and changed spec §3 in v0.4 so the spec and workbook agree.

## Finding 4 — spec gaps found while building

**What I checked.** Whether the spec alone was enough to build every contract item without guessing.

**What I found.**
- **No home for the checks.** Contract item 7 requires the §7 checks live in the workbook, but the spec's eight tabs had nowhere to put them.
- **Unnamed grid parameters.** Contract item 6 requires formula-driven rows, but the spec gave "±5%, 1% steps" with no named cells, which invites typed rows.
- **Check figures tied to one set of inputs.** The spec did not say that the §7 figures are base-case values (Finding 6).

**What I did.** Added a **Checks** tab (§3), named `STEP_FRAC`, `GRID_HALF_STEPS` and `PARITY_TOL` (§2, §6), and added the regression note to §7. Rebuilt the workbook from the updated spec.

## Finding 5 — no hard-codes; named ranges attached correctly (confirmed)

**What I checked.** A script over every cell: typed numbers by sheet and fill color, formulas by fill color, and the target and row label of each named range.

**What I found.**
- 292 formulas. 50 typed numbers, all on yellow or blue cells: 19 on `Inputs` and 31 on `Checks` (expected figures and tolerances). Zero typed numbers on `Forward`, `Money-Market`, `Options` and `Sensitivity`.
- All eleven contract names (`FC_AMT` through `BASIS`) point to `Inputs!C5:C15`, each beside its own label.
- `K_PUT` and `K_CALL` are blue cells holding `=S0_in`, by design.
- Recalculation reports 0 errors. Sensitivity has 11 rows with no typed values. The chart plots four series from those rows and shows the put's kink at the strike.

**What I did.** Nothing to fix.

## Finding 6 — responds to inputs; check figures are base-case values

**What I checked.** Changed one input at a time on a copy and recalculated.

| Change | Result |
|---|---|
| `S0_in` = 1.20 | Grid moves to 1.1400–1.2600, strike to 1.20, put floor to USD 3,530,553 |
| `STEP_FRAC` = 2% | Grid widens to 1.0093–1.2335 |
| `T_DAYS` = 180 | Growth factors, money market and put floor all move |
| `R_FC` = 6% | Money market falls to USD 3,325,401 |

No change produced an error.

**What I found.**
- Because the expected figures on `Checks` are the spec's base-case values, any input change makes several checks FAIL (9 after `S0_in` = 1.20, 11 after `T_DAYS` = 180). These are not model errors. Identity checks 2d, 3b and 4b still pass for any inputs.
- `R_USD` and `R_FC` are typed at their rounded 273-day values, so they do not follow `T_DAYS`. Checks 9a and 9b catch this (both FAIL when `T_DAYS` changes, and 9a FAILs when `BASIS` changes).

**What I did.** Added the regression note to spec §7. At Stage 4, recompute the expected column from the new inputs and re-derive `R_USD` and `R_FC` for the same tenor.

## Observation for Stage 5

The Stage 1 stress figures (USD 176,000 for a 5% fall and USD 317,000 for 9%) are 5% and 9% of the forward proceeds. In this workbook a 5% fall in `S_T` from the 1.1214 spot gives unhedged proceeds of USD 3,195,990, USD 330,810 below the forward. Measured against unhedged proceeds at spot (USD 3,364,200), the loss is USD 168,210. The memo should say which baseline it uses.

## Reproduce in Excel (author completed)

1. **Formulas → Name Manager:** confirm the eleven contract names point to `Inputs!C5:C15`.
2. **Change `S0_in`** on `Inputs` and confirm the Sensitivity table, strikes and chart move.
3. **Set `F0_in` to 1.1321:** confirm the parity flag on `Money-Market` changes to PASS.
4. **Open `Checks`:** confirm one FAIL (3c) at the current inputs.
5. **Ctrl+`** (show formulas) on `Forward`, `Money-Market`, `Options` and `Sensitivity`: confirm no typed values.
