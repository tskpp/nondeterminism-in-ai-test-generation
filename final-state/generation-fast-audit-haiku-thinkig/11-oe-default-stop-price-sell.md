# Summary
`Sell` `Stop` - Default `Order Price` from bid minus distance, frozen after open

# Traceability
- Test List: [AC-11]
- Requirement: DX-WEB-095, DX-WEB-086

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry UI, default pending price

# Preconditions
1. The platform displays a logged-in workspace.
2. Order Entry is available for an instrument that streams live bid and ask.
3. The platform exposes `pip_size`, `price_increment`, and Stop/Limit distance for the instrument via metadata, admin UI, or contract specification [Valid: values sufficient for the formula in Peculiarities].

# Actions
1. Read Ask, Bid, `pip_size`, `price_increment`, and Stop/Limit distance from the quote strip and instrument metadata [Valid: snapshot immediately before selecting `Stop`].
2. Select `Sell` on the order form.
3. Select `Stop` in the Order type control.
4. Read the numeric value in the `Order Price` field.
5. Wait until the streamed Ask and Bid both differ from the values read in step 1 [Valid: at least one full quote update on both sides].
6. Read the numeric value in the `Order Price` field again.

# Results
- The value from step 4 equals the Bid read in step 1 minus `default_distance` from Peculiarities, stepped to the instrument `price_increment` grid per platform default rules; the UI shows that value on the `price_increment` ladder (rounding detail in [AC-14c] when the raw default is not on-grid).
- The value from step 6 equals the value from step 4.

# Options
- None.

# Peculiarities
- `default_delta` = 1.5 × |Ask − Bid| / `pip_size`, evaluated with Ask, Bid, and `pip_size` from step 1.
- `default_distance` = (Stop/Limit distance × `pip_size`) + (`default_delta` × max(`pip_size`, `price_increment`)), using Stop/Limit distance and `price_increment` from step 1.
