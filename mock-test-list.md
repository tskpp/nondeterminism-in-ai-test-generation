# QA Test List

**Global Component Name**: OTC Order Entry
**Global Test Items**: Order Entry Form, Validation Engine
**Standard Preconditions**:
1. User is logged into the trading platform.
2. Order Entry popup is open for an instrument.

---

* `[AC-10]` **Verify default Stop price for Buy Stop uses ask plus default distance (where default distance = (Stop/Limit distance * pip_size) + (1.5 * |Ask - Bid| / pip_size) * max(pip_size, price_increment)) and does not re-tick with live quotes after OE open.**
  - *Maps to: DX-WEB-095, DX-WEB-086*
* `[AC-11]` **Verify default Stop price for Sell Stop uses bid minus default distance and freezes after open.**
  - *Maps to: DX-WEB-095*
