# Summary
Order Price - Default Stop populated for Buy

**Traceability:** `[AC-10]`

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry Form, Validation Engine

# Preconditions
1. User is logged into the trading platform.
2. Order Entry popup is open for an instrument.
3. The selected instrument has an active ask quote and a configured default distance.

# Actions
1. Select the `Buy` side.
2. Switch the Order type to `Stop` `[Valid: Instrument with ask quote and valid distance config]`.
3. Wait for the live market quotes to update.

# Results
- The system populates the Order Price field with a default value equal to the current ask price (at the exact moment of switching to Stop) plus the configured default distance.
- The system freezes the populated Order Price so it does not re-tick when subsequent live market quotes arrive.

# Options
None

# Peculiarities
- Default distance follows Confluence default-distance rules.