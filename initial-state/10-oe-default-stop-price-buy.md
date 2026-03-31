# Summary
Calculate default Buy Stop price

# Traceability
- Test List: `[AC-10]`
- Requirement: `DX-WEB-095`, `DX-WEB-086`

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry UI, Default Price Calculation

# Preconditions
1. User is logged into the trading platform.
2. Order Entry popup is open.
3. Live bid and ask prices are streaming.

# Actions
1. Select the 'Buy' side. [Valid: standard marginal FX or CFD instrument]
2. Observe the current Ask and Bid.
3. Switch the Order type to 'Stop'.
4. Observe the Order Price field when it first appears.
5. Wait for live bid and ask quotes to change.

# Results
- The Order Price field shows a value equal to the Ask at the instant of switching to 'Stop', plus default_distance.
- The Order Price value stays fixed when live bid and ask quotes change.

# Options
- Display rounding for the default Stop price is covered under `[AC-14a]`.

# Peculiarities
- Uses explicit distance variables D, P, I.