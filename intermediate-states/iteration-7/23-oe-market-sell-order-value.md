# Summary
Order Value - Market Sell notional uses bid with quantity formula

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
5. The account’s **base currency** and its **display precision** (minor units) for monetary fields are known to the tester.

# Actions
1. Select the `Sell` side. [Valid: same instrument as in Preconditions]
2. Confirm the `Order type` is `Market`. [Valid: default new-order type `Market`]
3. Input a lot quantity in the size control. [Valid: strictly positive value that satisfies min/max lot rules and **Min Order Size Increment** for the instrument]
4. Record **QtyLots**, **LotSize**, and **InstrumentMultiplier** from the sources in Preconditions at a single chosen instant.
5. Record **Bid** as the **Current Bid Price** shown on the Order Entry form for the `Sell` side at that same instant. [Valid: live quote present]
6. Record the numeric value shown in the read-only **Order Value** field (in account base currency) at that same instant.
7. Compute **N** per **Peculiarities**, apply the conversion **C** from instrument value currency to account base currency using an environment-approved reference for the snapshot instant, then format to account base currency precision to obtain the expected **Order Value**.
8. Compare the recorded **Order Value** from step 6 to the value from step 7.

# Results
- The **Order Value** from step 6 equals the expected value from step 7.
- The **Order Value** display uses **account base currency** precision (minor-unit count matches the known account base currency precision).

# Options
- Symmetric **Market Buy** coverage with **Ask** is under `[AC-22]`.
- Live recalculation when **Bid** or size changes is under `[AC-26]`; tiered quote selection by size is under `[AC-08]`.

# Peculiarities
Let **Q** = **QtyLots**, **LS** = **LotSize**, **M** = **InstrumentMultiplier**, **B** = **Current Bid Price** (the **Bid** recorded in Actions).

**Instrument-currency notional (before account conversion):**

**N** = **Q** × **LS** × **M** × **B**

Per `requirement.md`, **Order Value for Market Sell Orders** = **Q** × **LS** × **M** × **B**, then the result is **converted from the instrument’s value currency into the account base currency**. The captured requirement text does not define the conversion rate or method; **C** denotes that conversion mapping from **N** in instrument terms to account base currency at the UI snapshot instant.

**Order Value** field precision = **Account Base Currency precision** (`requirement.md`).
