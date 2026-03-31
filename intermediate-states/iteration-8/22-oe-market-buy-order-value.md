# Summary
Order Value - Market Buy notional uses ask with quantity formula

**Traceability:**
- Test List: `[AC-22]`
- Requirement: `DX-WEB-097` / Order Value in Account Currency (`requirement.md` — Calculation Rules, Market Buy)

# Priority
High

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry Form, Order Value display

# Preconditions
1. User is logged into the trading platform.
2. Order Entry popup is open for a **new** opening order on an instrument.
3. The instrument is a standard marginal FX or CFD instrument (not Spread Bet, Direct Exchange, Net-based FX, or other excluded extensions per session test-list assumptions).
4. The tester can read **QtyLots** (order size in lots), **LotSize**, and **InstrumentMultiplier** from the trading UI, instrument metadata, or an approved configuration source for that symbol.
5. The account’s **base currency** is known to the tester.
6. At the recording instant, **Current Ask** and **Current Bid** differ so that **Q** × **LS** × **M** × **Ask** and **Q** × **LS** × **M** × **Bid** produce different instrument-currency notionals before conversion.

# Actions
1. Select the `Buy` side. [Valid: same instrument as in Preconditions]
2. Confirm the `Order type` is `Market`. [Valid: default new-order type `Market`]
3. Input a lot quantity in the size control. [Valid: strictly positive value that satisfies min/max lot rules and **Min Order Size Increment** for the instrument]
4. Record **QtyLots**, **LotSize**, and **InstrumentMultiplier** from the sources in Preconditions at a single chosen instant.
5. Record **Ask** as the **Current Ask Price** shown on the Order Entry form for the `Buy` side at that same instant. [Valid: live quote present]
6. Record **Bid** as the **Current Bid Price** shown for the `Sell` side at that same instant.
7. Record the numeric value shown in the read-only **Order Value** field (in account base currency) at that same instant.
8. Compute **N_ask** per **Peculiarities**, apply the conversion **C** from instrument value currency to account base currency using an environment-approved reference for the snapshot instant, then format to account base currency precision to obtain the expected **Order Value** from the ask-based notional.
9. Compute **N_bid** = **Q** × **LS** × **M** × **Bid** (same **Q**, **LS**, **M**, **Bid** as recorded), apply the same **C** and the same formatting rules as in step 8 to obtain the bid-based counterfactual amount in account base currency.
10. Compare the recorded **Order Value** from step 7 to the value from step 8.
11. Compare the recorded **Order Value** from step 7 to the value from step 9.

# Results
- The **Order Value** from step 7 equals the expected value from step 8.
- The **Order Value** from step 7 does not equal the counterfactual value from step 9.

# Options
- Symmetric **Market Sell** coverage with **Bid** is under `[AC-23]`.
- **Order Value** display precision versus account base currency is under `[AC-25]`.
- Cross-currency conversion consistency with the displayed quote set is under `[AF-09]`.
- Live recalculation when **Ask** or size changes is under `[AC-26]`; tiered quote selection by size is under `[AC-08]`.

# Peculiarities
Let **Q** = **QtyLots**, **LS** = **LotSize**, **M** = **InstrumentMultiplier**, **A** = **Current Ask Price** (the **Ask** recorded in Actions).

**Instrument-currency notional (before account conversion) for Market Buy:**

**N_ask** = **Q** × **LS** × **M** × **A**

Per `requirement.md`, **Order Value for Market Buy Orders** = **Q** × **LS** × **M** × **A**, then the result is **converted from the instrument’s value currency into the account base currency**. The captured requirement text does not define the conversion rate or method; **C** denotes that conversion mapping from **N_ask** in instrument terms to account base currency at the UI snapshot instant.

**Order Value** field precision = **Account Base Currency precision** (`requirement.md`).
