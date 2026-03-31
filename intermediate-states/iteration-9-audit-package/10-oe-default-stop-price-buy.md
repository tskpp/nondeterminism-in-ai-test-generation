# Summary
Buy Stop default Stop price - Verify Ask-based default distance and quote freeze

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
2. Order Entry is open for an instrument that streams live Ask and Bid on the quote surface while the popup stays open.
3. For that instrument, the platform exposes `pip_size`, `price_increment`, and Stop/Limit distance on the quote surface, instrument details, or contract specification the Order Entry session uses.

# Actions
1. Record Ask, Bid, `pip_size`, `price_increment`, and Stop/Limit distance from the quote surface and instrument or contract display [Valid: snapshot taken before selecting order side and `Stop` order type].
2. Select `Buy` on the order form.
3. Select `Stop` as the order type.
4. Record the numeric value shown in the `Stop` price field.
5. Wait until streamed Ask and Bid both differ from the values recorded in step 1 [Valid: at least one full live quote update is observed on both Ask and Bid].
6. Record the numeric value shown in the `Stop` price field again.

# Results
- The `Stop` price field value from step 4 matches Ask from step 1 plus default_distance within one display tick of the instrument, where default_distance is defined in Peculiarities using Ask, Bid, `pip_size`, `price_increment`, and Stop/Limit distance from step 1, and the expected default Buy Stop price is aligned to the instrument price grid per implementation.
- The `Stop` price field value from step 6 equals the value from step 4.

# Options
None

# Peculiarities
default_distance = (Stop/Limit distance × pip_size) + (1.5 × |Ask − Bid| / pip_size) × max(pip_size, price_increment), evaluated with Ask, Bid, pip_size, price_increment, and Stop/Limit distance from the snapshot in Actions step 1. Expected default Buy Stop price uses that Ask plus default_distance, aligned to the instrument price grid per implementation.
