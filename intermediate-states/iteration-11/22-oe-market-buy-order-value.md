# Summary

For a **Market Buy** opening order, the Order Entry **Order Value** must equal the instrument-currency notional **QtyLots × LotSize × InstrumentMultiplier × Current Ask**, then converted from the instrument's value currency into the **account base currency** using the platform's conversion rules at the UI snapshot. This test validates that the displayed value follows the **ask** leg of the formula (not the bid) by comparing the UI **Order Value** to an ask-based expected amount and to a bid-based counterfactual at the same instant. Scope is standard marginal **FX/CFD-style** instruments per session assumptions; excluded instrument families (e.g. Spread Bet, Direct Exchange, Net-based FX) are out of scope.

**Traceability:**

- Test List: `qa-sessions/web-otc-order-entry/test-list.md` — `[AC-22]`
- Requirement: `DX-WEB-097` / Order Value in Account Currency (`qa-sessions/web-otc-order-entry/requirement.md` — Calculation Rules, **Market Buy** / Order Value section)

# Component

OTC Order Entry

# Severity

Functional

# Test Items

Order Entry Form, **Order Value** display (read-only), **Current Ask** / **Current Bid** as shown for the active symbol and side context

# Preconditions

1. User is logged into the trading platform with an account whose **base currency** is known.
2. **Order Entry** is open for a **new** opening order on a tradable instrument.
3. The instrument is a **standard marginal FX or CFD** instrument (not Spread Bet, Direct Exchange, Net-based FX, or other excluded extensions per `test-list.md` working assumptions).
4. **QtyLots** (order size in lots), **LotSize**, and **InstrumentMultiplier** are obtainable from the trading UI or instrument metadata for that symbol.
5. **Order type** can be set or confirmed as **Market**; **Buy** side is selectable.

# Actions

1. Select the **Buy** side. [Valid: same instrument as in Preconditions; OE remains on new opening order]
2. Set or confirm **Order type** is **Market**. [Valid: matches `[AC-06]` default **Market** for new order where applicable]
3. Enter a **lot quantity** in the size control. [Valid: strictly **positive**; satisfies min/max lot rules and **Min Order Size Increment** for the instrument]
4. At a **single chosen instant**, record **Q** = **QtyLots**, **LS** = **LotSize**, and **M** = **InstrumentMultiplier** from the sources in Preconditions (no mixing values from different timestamps).
5. At that **same instant**, record **A** = **Current Ask Price** shown on Order Entry for the **Buy** / market context. [Valid: live quote present; if tiered pricing applies per `[AC-08]`, the **Ask** used for sizing-sensitive displays must be the one shown for the **current** lot size]
6. At the **same instant**, record **Bid** = **Current Bid Price** shown for the **Sell** side (or the paired bid for the symbol per UI layout).
7. At the **same instant**, record the numeric **Order Value** shown in the read-only **Order Value** field, including its **currency** (must be **account base currency**).

# Results

- Compute **N_ask** = **Q × LS × M × A** using the formula from Peculiarities. Apply conversion **C** from instrument value currency to account base currency using an environment-approved reference rate/method for the snapshot instant. Format to account base currency display rules. The recorded **Order Value** from step 7 **equals** this computed value (per agreed tolerance / formatting rules).

# Options

- Symmetric **Market Sell** with **Bid** is `[AC-23]`.
- **Order Value** display **precision** vs account base currency is `[AC-25]`.
- Cross-currency conversion consistency with the displayed quote set is `[AF-09]`; fixture reconciliation is `[IG-05]`.
- Live recalculation when **Ask** or size changes is `[AC-26]`; tiered quote selection by size is `[AC-08]`.
- **Current Price** display rules (bid/ask per side) are `[AC-07]`; **Size** description for FX/CFD is `[AC-20]` / `[AC-21]`.

# Peculiarities

**Symbols (aligned with AC-22 header):**

- **Q** = **QtyLots** (lots entered in the size control)
- **LS** = **LotSize**
- **M** = **InstrumentMultiplier** (same as **Multiplier** in the test-list header)
- **A** = **Current Ask Price** at the UI snapshot (the **Ask** recorded in Action 5)
- **B** = **Current Bid Price** at the UI snapshot (the **Bid** recorded in Action 6)

**Instrument-currency notional (before conversion to account base) for Market Buy:**

**N_ask** = **Q × LS × M × A**

Per `requirement.md` (Order Value / **Market Buy**), **Order Value** = **Q × LS × M × A** in instrument terms, then **converted from the instrument's value currency into the account base currency**. The captured requirement text does not define the conversion rate or method; **C** denotes that mapping from **N_ask** (instrument currency) to account base currency at the snapshot instant—obtain **C** from an **environment-approved** source (e.g. risk/pricing reference, approved calculator, or `[IG-05]` fixture procedure).

**Counterfactual Validation (Optional):**

If testing in an environment where **Ask ≠ Bid**, you may optionally compute **N_bid** = **Q × LS × M × B** using the recorded **Bid** and apply the same conversion **C** and formatting rules. Then verify that the recorded **Order Value** does NOT equal this bid-based counterfactual value. This optional check proves the implementation uses **Ask** specifically (not **Bid**), but is not required if **Ask** and **Bid** are equal in your environment.

**Edge / definition notes:**

- If **account base currency** equals the instrument **value currency**, **C** may be identity; the test still asserts the **Q × LS × M × A** leg correctly.
- **Order Value** field precision is governed by **account base currency** rules; do not fold a separate precision assertion here—defer to `[AC-25]` for precision-focused checks.

