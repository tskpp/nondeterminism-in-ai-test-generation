# Iteration-11 Critical Fixes Applied - Verification Report

**Date:** 2026-03-31  
**Status:** ✅ ALL IMMEDIATE FIXES APPLIED

---

## Summary of Fixes Applied

All 6 immediate recommendations from the audit have been applied to both AC-22 and AC-23 test cases:

1. ✅ **Removed Ask ≠ Bid precondition blocker**
2. ✅ **Reduced preconditions from 7 to 5**
3. ✅ **Moved computations from Actions 8-11 to Results**
4. ✅ **Simplified Results to single primary assertion**
5. ✅ **Normalized AC-23 traceability to match AC-22**
6. ✅ **Aligned precondition phrasing and defined symbols symmetrically**

---

## Detailed Changes Per Test Case

### AC-22: Market Buy Order Value

**File:** `22-oe-market-buy-order-value.md`

#### Fix 1: Removed Ask ≠ Bid Blocker
```
BEFORE: Precondition 6 "At the recording instant, Ask ≠ Bid so that..."
AFTER:  [Deleted] - No longer a blocking precondition
```
**Impact:** Test can now execute when Ask = Bid (zero spread); removed false negative.

#### Fix 2: Reduced Preconditions (7 → 5)
```
BEFORE: 7 preconditions (including redundant "live quotes available" and explicit documentation requirement)
AFTER:  5 core preconditions:
  1. User logged in + base currency known
  2. OE open for new order
  3. Instrument type (standard FX/CFD)
  4. Metadata available (Q, LS, M)
  5. Order type/side selectable
```
**Impact:** Streamlined setup; removed unnecessary verification lines.

#### Fix 3: Moved Computations to Results
```
BEFORE: Actions 8-11 contained Compute, Compute, Compare, Compare
  8. Compute N_ask = Q × LS × M × A
  9. Compute N_bid = Q × LS × M × Bid
  10. Compare recorded vs expected
  11. Compare recorded vs counterfactual

AFTER: Actions 1-7 UI-only (no cognitive steps)
  7. Record Order Value [end of Actions]
  [All computation logic moved to Results]
```
**Impact:** Clean action/result separation; computations now only in Results section.

#### Fix 4: Simplified Results to Single Assertion
```
BEFORE (2 results):
  - Result 1: Order Value matches ask-based expected value
  - Result 2: Order Value does NOT match bid-based counterfactual

AFTER (1 result):
  - Compute N_ask = Q × LS × M × A, apply conversion C, format.
    The recorded Order Value equals this computed value.
```
**Impact:** Clear primary assertion; counterfactual moved to Peculiarities as optional.

#### Fix 5: Peculiarities Updated
```
BEFORE: Reference to "Ask ≠ Bid" in edge notes
AFTER: 
  - Added explicit symbol definitions (Q, LS, M, A, B, C, N_ask, N_bid)
  - Added "Counterfactual Validation (Optional)" section explaining:
    * When Ask ≠ Bid, may optionally verify value ≠ N_bid
    * Optional check (not required)
    * Purpose: prove Ask is used, not Bid
```
**Impact:** Counterfactual moved from blocking to optional; proper context.

**Traceability:** ✓ Correct (no changes needed)  
**Test Items:** ✓ Correct (detailed description present)  
**Final Precondition Count:** 5 ✅

---

### AC-23: Market Sell Order Value

**File:** `23-oe-market-sell-order-value.md`

#### Fix 1: Aligned Test Items
```
BEFORE: "Order Entry Form, Order Value display"
AFTER:  "Order Entry Form, Order Value display (read-only), 
         Current Ask / Current Bid as shown for the active symbol 
         and side context"
```
**Impact:** Matches AC-22 detail level; symmetric description.

#### Fix 2: Normalized Traceability
```
BEFORE: 
  - Test List: [AC-23]
  - Requirement: DX-WEB-097 / ... (shortened, abbreviated paths)

AFTER:
  - Test List: qa-sessions/web-otc-order-entry/test-list.md — [AC-23]
  - Requirement: DX-WEB-097 / Order Value in Account Currency 
                 (qa-sessions/web-otc-order-entry/requirement.md — 
                 Calculation Rules, Market Sell / Order Value section)
```
**Impact:** Matches AC-22 format exactly; full paths and context.

#### Fix 3: Reduced Preconditions (7 → 5)
```
BEFORE: 7 preconditions with "Order Entry popup" and "tester can read"
AFTER:  5 core preconditions mirroring AC-22:
  1. User logged in + base currency known
  2. Order Entry open for new order (removed "popup" → consistent "Order Entry")
  3. Instrument type (standard FX/CFD)
  4. Metadata available (changed "tester can read" → "are obtainable")
  5. Order type/side selectable
```
**Impact:** Reduced bloat; fixed tester-knowledge language; symmetric with AC-22.

#### Fix 4: Aligned Actions (Buy → Sell substitution)
```
BEFORE: Actions phrased differently; mixed formatting (backticks vs bold)
  1. Select the `Sell` side
  2. Confirm the `Order type` is `Market`
  4. Record QtyLots... [no explicit variable assignment]
  5. Record Bid as... [abbreviated variable reference]

AFTER: Mirrored AC-22 structure with Sell/Bid substitutions:
  1. Select the **Sell** side [same as AC-22 Buy, formatting consistent]
  2. Set or confirm **Order type** is **Market** [same wording as AC-22]
  4. At a single chosen instant, record Q, LS, M... [explicit variable assignments]
  5. At that same instant, record **B** = Current Bid Price... [clear variable symbol]
```
**Impact:** Perfect symmetry; consistent terminology; clear variable naming.

#### Fix 5: Removed Computational Steps from Actions
```
BEFORE: Actions 8-11 contained Compute, Compute, Compare, Compare
AFTER:  Actions 1-7 UI-only; removed steps 8-11 entirely
```
**Impact:** Clean separation; all logic in Results.

#### Fix 6: Simplified Results to Single Assertion
```
BEFORE (2 results):
  - Result 1: Order Value equals expected bid-based value
  - Result 2: Order Value does NOT equal ask-based counterfactual

AFTER (1 result):
  - Compute N_bid = Q × LS × M × B, apply conversion C, format.
    The recorded Order Value equals this computed value.
```
**Impact:** Single clear assertion; counterfactual optional in Peculiarities.

#### Fix 7: Updated Peculiarities with Full Symmetry
```
BEFORE: Less detailed than AC-22; didn't define N_ask prominently
AFTER: 
  - Full symbol list (Q, LS, M, B, A, C, N_bid, N_ask)
  - "Counterfactual Validation (Optional)" with parallel structure to AC-22
  - Clear definitions for both N_bid (primary) and N_ask (counterfactual)
  - Edge notes identical to AC-22 except for currency mention
```
**Impact:** Perfect symmetry with AC-22; all symbols defined.

**Final Precondition Count:** 5 ✅

---

## Before/After Comparison

| Metric | Before | After | Status |
|--------|--------|-------|--------|
| **AC-22 Preconditions** | 7 (with blocker) | 5 (no blocker) | ✅ Fixed |
| **AC-23 Preconditions** | 7 (with blocker) | 5 (no blocker) | ✅ Fixed |
| **AC-22 Ask≠Bid Blocker** | Yes (blocking) | No (optional) | ✅ Fixed |
| **AC-23 Ask≠Bid Blocker** | Yes (blocking) | No (optional) | ✅ Fixed |
| **AC-22 Actions** | 11 (include compute/compare) | 7 (UI only) | ✅ Fixed |
| **AC-23 Actions** | 11 (include compute/compare) | 7 (UI only) | ✅ Fixed |
| **AC-22 Results** | 2 assertions | 1 assertion | ✅ Fixed |
| **AC-23 Results** | 2 assertions | 1 assertion | ✅ Fixed |
| **AC-22/AC-23 Traceability** | Different format | Identical format | ✅ Fixed |
| **AC-22/AC-23 Test Items** | Different detail | Identical detail | ✅ Fixed |
| **AC-22/AC-23 Precondition Wording** | Divergent ("Order Entry" vs "popup", "obtain" vs "read") | Identical ("Order Entry", "are obtainable") | ✅ Fixed |
| **Counterfactual Location** | In Results (mandatory) | In Peculiarities (optional) | ✅ Fixed |
| **Symbol Definitions** | Incomplete in AC-23 | Complete + symmetric | ✅ Fixed |

---

## Validation Checklist

### Critical Issues (Now Resolved)
- ✅ Ask ≠ Bid is no longer a hard precondition
- ✅ Actions contain only UI interactions (steps 1-7)
- ✅ No Compute/Compare verbs in Actions section
- ✅ Results contain only primary assertion
- ✅ Counterfactual note moved to Peculiarities (optional)

### Major Issues (Now Resolved)
- ✅ Traceability format identical (AC-22 and AC-23)
- ✅ Test Items description identical
- ✅ Precondition wording aligned ("Order Entry" not "popup", "are obtainable")
- ✅ Emphasis formatting consistent (bold for UI elements)

### Medium Issues (Now Resolved)
- ✅ Preconditions reduced from 7 to 5
- ✅ No "tester can/must read/obtain" language (now "are obtainable")
- ✅ Peculiarities symbols defined consistently (Q, LS, M, A, B, C, N_ask, N_bid)
- ✅ [Valid:] tags present and consistent in Action steps

### Symmetry Validation (AC-22 vs AC-23)
- ✅ Identical precondition count (5 each)
- ✅ Identical action pattern (7 steps, mirrored only on Sell/Bid)
- ✅ Identical results phrasing (single assertion)
- ✅ Identical field naming ("Order Value")
- ✅ Identical traceability format
- ✅ Identical Peculiarities structure (only Q×LS×M×B vs Q×LS×M×A)

---

## File Statistics

| File | Before | After | Change |
|------|--------|-------|--------|
| 22-oe-market-buy-order-value.md | 80 lines | 76 lines | -4 lines (removed Actions 8-11) |
| 23-oe-market-sell-order-value.md | 62 lines | 69 lines | +7 lines (added Counterfactual section + definitions) |

**Total:** 142 lines → 145 lines (net +3 lines, but distribution improved)

---

## Ready for Next Audit

Both test cases now:
- ✅ Pass all 6 immediate recommendations
- ✅ Have symmetric structure (Buy/Sell)
- ✅ Have clean action/result separation
- ✅ Have optional counterfactual validation
- ✅ Have proper precondition scope (5 core items)
- ✅ Have no false negatives (Ask ≠ Bid blocker removed)

**Recommendation:** Re-run audit to verify all critical/major/medium issues are resolved before sign-off.

---

## Next: Final Re-Audit

Prepare for `iteration-11-final-audit` to confirm:
1. All 6 immediate recommendations passed
2. No critical issues remain
3. Symmetry validation complete
4. Ready for test execution

**Status:** ✅ FIXES COMPLETE - READY FOR FINAL AUDIT
