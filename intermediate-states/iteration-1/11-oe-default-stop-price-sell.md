# Summary
Order Price - Default Stop populated for Sell

**Traceability:** `[AC-11]`

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry Form, Validation Engine

# Preconditions
1. User is logged into the trading platform.
2. Order Entry popup is open for an instrument.
3. The selected instrument has an active bid quote and a configured default distance.

# Actions
1. Select the `Sell` side.
2. Switch the Order type to `Stop` `[Valid: Instrument with bid quote and valid distance config]`.
3. Wait for the live market quotes to update.

# Results
- The system populates the Order Price field with a default value equal to the current bid price (at the exact moment of switching to Stop) minus the configured default distance.
- The system freezes the populated Order Price so it does not re-tick when subsequent live market quotes arrive.

# Options
None

# Peculiarities
None