# Summary
Order Value - Market Sell notional uses bid with the same quantity formula as Market Buy (lots × lot size × multiplier × side-appropriate price), then converts to account base currency. This is the symmetric Sell-side check to Market Buy’s ask-based Order Value.

**Traceability:**
- Test List: `[AC-23]`
- Requirement: `DX-WEB-097` / Order Value in Account Currency (`requirement.md` — Calculation Rules, Market Sell)

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
6. At the recording instant, **Current Ask** and **Current Bid** differ so that **Q** × **LS** × **M** × **Bid** and **Q** × **LS** × **M** × **Ask** produce different instrument-currency notionals before conversion.

# Actions
1. Select the `Sell` side. [Valid: same instrument as in Preconditions]
2. Confirm the `Order type` is `Market`. [Valid: default new-order type `Market`]
3. Input a lot quantity in the size control. [Valid: strictly positive value that satisfies min/max lot rules and **Min Order Size Increment** for the instrument]
4. Record **QtyLots**, **LotSize**, and **InstrumentMultiplier** from the sources in Preconditions at a single chosen instant.
5. Record **Bid** as the **Current Bid Price** shown on the Order Entry form for the `Sell` side at that same instant. [Valid: live quote present]
6. Record **Ask** as the **Current Ask Price** shown for the `Buy` side at that same instant.
7. Record the numeric value shown in the read-only **Order Value** field (in account base currency) at that same instant.
8. Compute **N_bid** per **Peculiarities**, apply the conversion **C** from instrument value currency to account base currency using an environment-approved reference for the snapshot instant, then format to account base currency precision to obtain the expected **Order Value** from the bid-based notional.
9. Compute **N_ask** = **Q** × **LS** × **M** × **Ask** (same **Q**, **LS**, **M**, **Ask** as recorded), apply the same **C** and the same formatting rules as in step 8 to obtain the ask-based counterfactual amount in account base currency.
10. Compare the recorded **Order Value** from step 7 to the value from step 8.
11. Compare the recorded **Order Value** from step 7 to the value from step 9.

# Results
- The **Order Value** from step 7 equals the expected value from step 8.
- The **Order Value** from step 7 does not equal the counterfactual value from step 9.

# Options
- Symmetric **Market Buy** coverage with **Ask** is under `[AC-22]`.
- **Pending** Limit/Stop Order Value using the pending order price is under `[AC-24]`.
- **Order Value** display precision versus account base currency is under `[AC-25]`.
- Cross-currency conversion consistency with the displayed quote set is under `[AF-09]`.
- Live recalculation when **Bid** or size changes is under `[AC-26]`; tiered quote selection by size is under `[AC-08]`.

# Peculiarities
Let **Q** = **QtyLots**, **LS** = **LotSize**, **M** = **InstrumentMultiplier**, **B** = **Current Bid Price** (the **Bid** recorded in Actions).

**Instrument-currency notional (before account conversion) for Market Sell:**

**N_bid** = **Q** × **LS** × **M** × **B**

Per `requirement.md`, **Order Value for Market Sell Orders** uses the same quantity structure as Market Buy (**Q** × **LS** × **M**) multiplied by the **Current Bid Price** (**B**), then the result is **converted from the instrument’s value currency into the account base currency**. The captured requirement text does not define the conversion rate or method; **C** denotes that conversion mapping from **N_bid** in instrument terms to account base currency at the UI snapshot instant.

**Order Value** field precision = **Account Base Currency precision** (`requirement.md`).

**Definitions:** **Current Bid** / **Current Ask** align with **AC-07** (bid for Sell, ask for Buy) for the prices used in Order Value and displayed current price context.
