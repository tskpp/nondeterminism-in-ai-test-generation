# Summary
Order Price - Default stop populated for Buy

**Traceability:**
- Test List: `[AC-10]`
- Requirement: `DX-WEB-095`, `DX-WEB-086`

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry Form, Validation Engine

# Preconditions
1. User is logged into the trading platform.
2. Order Entry popup is open for an instrument.
3. The instrument’s Stop/Limit distance (in pips, denoted D), pip_size (denoted P), and price_increment (denoted I) are known to the tester from configuration or instrument metadata.
4. The instrument has active bid and ask quotes at the time of switching to `Stop`.

# Actions
1. Select the `Buy`/`Sell` side. [Valid: standard marginal FX or CFD instrument]
2. Record Ask, Bid, D, P, and I from the live quote and configuration immediately before switching order type. [Valid: P > 0, I > 0, D ≥ 0, Ask and Bid are the displayed quote values at record time]
3. Switch the `Order type` to `Stop`. [Valid: opening order flow with default order type `Market` before the switch]
4. Record the numeric value shown in the `Order Price` field when it first appears.
5. Wait for live bid and ask quotes to change while the Order Entry popup stays open.
6. Compare the recorded `Order Price` to the expected default price (A + default_distance), stepped to the `price_increment` grid.

# Results
- The `Order Price` field shows a value equal to the Ask at the instant of switching to `Stop`, plus default_distance computed per Peculiarities from Ask, Bid, D, P, and I at that instant.
- The `Order Price` value is unchanged from the numeric value recorded in step 4.

# Options
- Display rounding for the default Stop price is covered under `[AC-14a]` (Buy Stop rounds up to `price_increment`).

# Peculiarities
Use the quote snapshot at the instant the `Order type` becomes `Stop`. Let A = Ask and B = Bid from that snapshot. Let D = Stop/Limit distance in pips, P = pip_size, and I = price_increment.

Compute default_distance in price units as:

default_distance = (D × P) + ((1.5 × |A − B| / P) × max(P, I))

That is: first add (D × P) to the product of (1.5 × |A − B| / P) and max(P, I).

For a **Buy Stop** default pending price, the expected value before any separate display-rounding rule is:

expected_default_price = A + default_distance