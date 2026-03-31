# Summary
Default stop populated for Sell

**Traceability:**
- Test List: `[AC-11]`

# Component
Order Entry

# Severity
Functional

# Test Items
Order Entry Form

# Preconditions
1. User is logged into the trading platform.

# Actions
1. Open the Order Entry popup.
2. Select the `Sell` side.
3. Take note of the current Bid.
4. Select the `Stop` Order type.
5. Check the Order Price field.

# Results
The Order Price field should be correctly populated with the initial Bid value minus the calculated distance, and it should not change its value when the market quotes fluctuate while the form is open.

# Options
- Display rounding for the default Stop price is covered under `[AC-14c]`.

# Peculiarities