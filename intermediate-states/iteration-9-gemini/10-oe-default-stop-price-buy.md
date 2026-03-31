# Summary
OTC Order Entry — Verify default `Stop` for `Buy` `Stop` uses Ask plus default distance and stays fixed after live quotes change

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
3. The instrument has an active streaming quote feed; Ask and Bid prices update on the quote display while the Order Entry popup stays open.
4. Platform or contract data exposes Stop/Limit distance, pip_size, and price_increment for the instrument (on-screen fields, instrument details, or specification panel).

# Actions
1. With the Order Entry popup open, select `Buy` as the order direction [Valid: any instrument meeting preconditions].
2. Select `Stop` as the order type [Valid: `Stop` available for the instrument].
3. Record Ask, Bid, Stop/Limit distance, pip_size, and price_increment from the quote display and instrument details at this moment [Valid: non-stale snapshot immediately after steps 1–2].
4. Record the numeric value in the `Stop` price field.
5. Wait until the quote display shows at least two updates to Ask or Bid after step 4 [Valid: any sequence of live ticks].
6. Record the numeric value in the `Stop` price field again.

# Results
- The value from step 4 equals the Ask from step 3 plus default_distance, with default_distance = (Stop/Limit distance × pip_size) + (1.5 × |Ask − Bid| / pip_size) × max(pip_size, price_increment), using the Ask, Bid, Stop/Limit distance, pip_size, and price_increment recorded in step 3.
- The value from step 6 equals the value from step 4.

# Options
None

# Peculiarities
Default distance for this check is defined as: default_distance = (Stop/Limit distance × pip_size) + (1.5 × |Ask − Bid| / pip_size) × max(pip_size, price_increment). The expected default `Stop` for a `Buy` `Stop` is Ask + default_distance, evaluated from the quote and contract inputs recorded when the `Stop` field is first observed (step 4).