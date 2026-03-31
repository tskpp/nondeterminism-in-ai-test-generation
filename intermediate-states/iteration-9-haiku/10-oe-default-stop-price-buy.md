# Summary
Buy Stop default `Stop` price - Verify Ask-based default distance and quote freeze

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
3. The instrument streams live Ask and Bid, and `pip_size`, `price_increment`, and Stop/Limit distance are known from product configuration or the UI for that symbol [Valid: values sufficient to compute default_distance in Peculiarities].

# Actions
1. Record Ask, Bid, `pip_size`, `price_increment`, and Stop/Limit distance from the quote surface and configuration [Valid: snapshot taken before selecting `Stop`].
2. Compute default_distance using the formula in Peculiarities with the values from step 1.
3. Compute expected_default_stop as Ask from step 1 plus default_distance from step 2, rounded or stepped per the instrument's displayed price grid [Valid: same rounding rules the Order Entry UI applies to default prices].
4. Select `Buy` on the order form.
5. Select `Stop` as the order type.
6. Record the numeric value shown in the `Stop` price field.
7. Compare the value from step 6 to expected_default_stop from step 3 [Valid: match within one display tick of the instrument].
8. Wait until streamed Ask and Bid both differ from the snapshot in step 1 [Valid: at least one full live quote update is observed].
9. Record the numeric value shown in the `Stop` price field again.

# Results
- The `Stop` price field value from step 6 matches expected_default_stop from step 3 within one display tick.
- The `Stop` price field value from step 9 equals the value from step 6.

# Options
None

# Peculiarities
default_distance = (Stop/Limit distance * pip_size) + (1.5 * |Ask - Bid| / pip_size) * max(pip_size, price_increment), evaluated with Ask, Bid, pip_size, price_increment, and Stop/Limit distance from the snapshot taken in Actions step 1. Expected default Buy `Stop` price uses that Ask plus default_distance, aligned to the instrument price grid per implementation.
