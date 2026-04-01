# Summary
Sell Stop default Order Price - Matches Bid minus default distance and stays fixed after live quote updates

# Traceability
- Test List: [AC-11]
- Requirement: DX-WEB-095, DX-WEB-086

# Component
OTC Order Entry

# Severity
Functional

# Test Items
Order Entry UI

# Preconditions
1. The platform displays a logged-in workspace.
2. Order Entry is open for an instrument that streams live bid and ask.
3. The platform exposes `pip_size`, `price_increment`, and Stop/Limit distance for the instrument via metadata, admin UI, or contract specification [Valid: values sufficient for the formulas in Peculiarities].

# Actions
1. Read Ask, Bid, `pip_size`, `price_increment`, and Stop/Limit distance from the quote strip and instrument metadata [Valid: snapshot immediately before selecting `Stop`].
2. Select `Sell` on the order form.
3. Select `Stop` in the Order type control.
4. Read the numeric value in the `Order Price` field.
5. Wait for streamed Ask and Bid values that both differ from the values read in step 1 [Valid: at least one full quote update on both sides].
6. Read the numeric value in the `Order Price` field again.

# Results
- The `Order Price` field displays the numeric value read in step 4; that value equals the Bid recorded in step 1 minus `default_distance` from Peculiarities, stepped to the instrument `price_increment` grid per tick alignment rules in [AC-14c].
- The `Order Price` field after step 6 displays the same numeric value as after step 4.

# Options
- None.

# Peculiarities
- Default raw `Order Price` for `Sell` `Stop` = Bid − `default_distance`; Bid is the value recorded in step 1.
- `default_delta` = 1.5 × |Ask − Bid| / `pip_size`, evaluated with Ask, Bid, and `pip_size` from step 1.
- `default_distance` = (Stop/Limit distance × `pip_size`) + (`default_delta` × max(`pip_size`, `price_increment`)), using Stop/Limit distance and `price_increment` from step 1.
- Off-grid raw defaults relative to `price_increment`: tick alignment for `Sell` `Stop` is defined in [AC-14c].
