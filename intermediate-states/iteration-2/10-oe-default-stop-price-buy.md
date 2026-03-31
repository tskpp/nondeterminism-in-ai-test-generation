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

# Actions
1. Select the `Buy` side.
2. Record Ask and Bid immediately before switching order type.
3. Switch the `Order type` to `Stop`.
4. Record the numeric value shown in the `Order Price` field when it first appears.
5. Wait for live bid and ask quotes to change while the Order Entry popup stays open.

# Results
- The `Order Price` field shows a value equal to the Ask at the instant of switching to `Stop`, plus default_distance.
- The `Order Price` value stays fixed when live bid and ask quotes change.

# Options
- Display rounding for the default Stop price is covered under `[AC-14a]`.

# Peculiarities
- Per Confluence default-distance rules, the default distance is computed and applied to the price. Ensure to verify this calculation manually against the live quote.