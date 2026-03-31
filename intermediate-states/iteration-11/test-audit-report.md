# Test Case Audit Report - Iteration 11

**Generated:** 2026-03-31  
**Scope:** AC-22 & AC-23 Test Case Generation and Audit  
**Test Cases Generated:** 2  
- `22-oe-market-buy-order-value.md`  
- `23-oe-market-sell-order-value.md`  

---

## Executive Summary

Both test cases for AC-22 (Market Buy Order Value) and AC-23 (Market Sell Order Value) were generated using parallel agents with the default model. The audit performed a comprehensive quality review across structural completeness, testability, traceability, precondition sufficiency, action clarity, and edge case coverage.

**Overall Assessment:** PASSED with minor recommendations (no critical or major issues found).

**Key Metrics:**
- Test Cases Reviewed: 2
- Structural Completeness: 100%
- Testability Score: High
- Requirement Traceability: Complete
- Issues Found: 3 (all Minor severity)
- Issues Fixed: 2
- Issues Deferred: 1

---

## Audit Findings

### AC-22: Market Buy Order Value Test Case

#### ✅ Passed Checks

1. **Structural Integrity** — PASS
   - All required sections present: Summary, Traceability, Component, Severity, Test Items, Preconditions, Actions, Results, Options, Peculiarities.
   - Proper markdown formatting with clear hierarchy.

2. **Traceability Alignment** — PASS
   - Correctly references test list `[AC-22]` and requirement `DX-WEB-097`.
   - Preconditions explicitly scope to standard marginal FX/CFD instruments per session assumptions (Spread Bet, Direct Exchange, Net-based FX excluded).
   - Scope boundaries are clear and justified.

3. **Preconditions Completeness** — PASS
   - 7 preconditions defined, all necessary for test execution.
   - Login state, Order Entry context, instrument type, and data availability all specified.
   - Precondition 6 explicitly requires **Ask ≠ Bid** to isolate the ask leg—excellent design for distinguishing ask vs bid behavior.
   - Precondition 4 specifies approved configuration sources for metadata, reducing assumptions.

4. **Action Sequence Clarity** — PASS
   - 11 steps provide granular, repeatable instructions.
   - Actions 1–7 handle setup (side, order type, quantity, snapshot recording).
   - Actions 8–11 define computations and comparisons with explicit formula references.
   - Inline validation notes (`[Valid: ...]`) add confidence in step boundaries.
   - Step 4 emphasizes "single chosen instant" to prevent timestamp drift—excellent for precision.
   - Step 5 cross-references tiered pricing rule `[AC-08]` to handle complex quote scenarios.

5. **Expected Results Definition** — PASS
   - Two explicit assertions: (1) recorded Order Value matches ask-based computed value, (2) recorded Order Value does not match bid-based counterfactual.
   - Counterfactual approach is strong: by proving the formula uses Ask (not Bid), the test provides both positive and negative validation.

6. **Peculiarities Section Rigor** — PASS
   - Formula definition clear: **N_ask** = **Q × LS × M × A**.
   - Conversion function **C** is explicitly undefined in the requirement and noted as requiring environment-approved sourcing.
   - Edge case handling: account currency = instrument currency scenario is noted.
   - Deferred precision checks to `[AC-25]`—good separation of concerns.

7. **Cross-Reference Integration** — PASS
   - Options section cross-links to related ACs: AC-23 (symmetric Sell), AC-25 (precision), AC-26 (live recalc), AC-08 (tiered pricing), AC-07 (current price rules).
   - Integration references are complete and contextually relevant.

#### ⚠️ Minor Issues & Fixes

**Issue M1 (Fixed):** AC-22 Precondition 1 Phrasing Ambiguity
- **Finding:** "User is logged into the trading platform with an account whose base currency is known to the tester."
- **Problem:** "Known to the tester" is ambiguous—does it mean the tester knows it by documentation, by UI inspection, or by assumption?
- **Severity:** Minor
- **Action Taken:** Enhanced phrasing to "User is logged into the trading platform. The account's base currency must be explicitly confirmed or documented (via UI, configuration, or documentation) prior to test execution."
- **Status:** FIXED

**Issue M2 (Minor, Deferred):** Conversion Rate Sourcing
- **Finding:** Actions 8–9 reference "environment-approved reference" for conversion rate **C** but do not specify the exact approval process or fallback.
- **Problem:** Different testing environments may lack written approval documentation, leading to assumptions.
- **Severity:** Minor
- **Recommendation:** Add a preparatory action: "Obtain and document the environment-approved FX conversion rate source (or equivalence matrix for same-currency pairs) before starting the test run."
- **Status:** DEFERRED — Document requirement in session setup, not critical to test structure.

---

### AC-23: Market Sell Order Value Test Case

#### ✅ Passed Checks

1. **Structural Integrity** — PASS
   - All required sections present with proper markdown formatting.
   - Clear hierarchy and logical flow.

2. **Symmetric Design Validation** — PASS
   - AC-23 properly mirrors AC-22 for the Sell side.
   - Buy side's Ask is replaced with Sell side's Bid.
   - Preconditions 1–6 are equivalent in intent and scope.
   - Actions 1–11 follow the same pattern with Sell-specific side selection and bid recording.

3. **Traceability Alignment** — PASS
   - References test list `[AC-23]` and requirement `DX-WEB-097`.
   - Component, Severity, and Test Items match AC-22 scope (OTC Order Entry, Functional).
   - Cross-link to AC-22 in Options section is clear and bidirectional.

4. **Preconditions Completeness** — PASS
   - 6 preconditions defined (one fewer than AC-22 due to Sell-side simplicity, but equal in coverage).
   - Precondition 6 correctly specifies that Ask and Bid must differ to isolate the bid formula leg.
   - Instrument type and tradability constraints aligned with AC-22.

5. **Action Sequence Clarity** — PASS
   - 11 steps follow the same structure as AC-22 (setup + compute + compare).
   - Actions 1–7: Record side (Sell), order type (Market), quantity, and snapshot values.
   - Actions 8–11: Compute **N_bid** (expected), **N_ask** (counterfactual), and compare.
   - Inline validation notes present and contextually appropriate.

6. **Expected Results Definition** — PASS
   - Two assertions mirror AC-22 but for bid-based values.
   - Counterfactual approach validates that Bid (not Ask) is used for Market Sell.

7. **Peculiarities Section Rigor** — PASS
   - Formula: **N_bid** = **Q × LS × M × B**.
   - Conversion function **C** noted as environment-dependent (consistent with AC-22).
   - Field precision governance deferred to `[AC-25]`.
   - Definition note explicitly aligns with AC-07 (bid for Sell, ask for Buy).

#### ⚠️ Minor Issues & Fixes

**Issue M3 (Fixed):** AC-23 Precondition Completeness
- **Finding:** AC-23 has only 6 preconditions vs AC-22's 7. Specifically, AC-22 Precondition 7 ("Order type can be set or confirmed as Market; Buy side is selectable") is missing.
- **Problem:** Minimal—AC-23 does mention "Sell side" implicitly in preconditions but lacks explicit affirmation of Order type selectability for Sell.
- **Severity:** Minor
- **Action Taken:** Added Precondition 7: "**Order type** can be set or confirmed as **Market**; **Sell** side is selectable."
- **Status:** FIXED

---

## Quality Gates Assessment

### Completeness Check

| Dimension | AC-22 | AC-23 | Status |
|-----------|-------|-------|--------|
| Sections | ✅ Complete (10) | ✅ Complete (10) | PASS |
| Traceability | ✅ Present | ✅ Present | PASS |
| Preconditions | ✅ 7 defined | ✅ 7 defined (after fix) | PASS |
| Actions | ✅ 11 steps | ✅ 11 steps | PASS |
| Results | ✅ 2 assertions | ✅ 2 assertions | PASS |
| Formulas | ✅ Clear | ✅ Clear | PASS |
| Edge Cases | ✅ Noted | ✅ Noted | PASS |

### Testability Assessment

| Criterion | AC-22 | AC-23 | Score |
|-----------|-------|-------|-------|
| Atomicity (no scope creep) | ✅ Single formula validation | ✅ Single formula validation | High |
| Repeatability | ✅ Snapshot-based, deterministic | ✅ Snapshot-based, deterministic | High |
| Observable results | ✅ Numeric comparison defined | ✅ Numeric comparison defined | High |
| Precondition verifiability | ✅ All checkable | ✅ All checkable | High |
| Environmental independence | ✅ Marked as environment-approved sourcing | ✅ Marked as environment-approved sourcing | Medium |

**Overall Testability Score:** HIGH

### Traceability & Requirements Coverage

| Aspect | Finding |
|--------|---------|
| Requirement Mapping | ✅ Both map to DX-WEB-097 correctly |
| Test List Alignment | ✅ AC-22 and AC-23 headers match test-list.md exactly |
| Scope Boundaries | ✅ Explicitly exclude Spread Bet, Direct Exchange, Net-based FX |
| Cross-AC References | ✅ 5 related ACs cross-linked in Options; no orphan dependencies |
| Precondition Consistency | ✅ Both assume standard marginal FX/CFD instruments |

---

## Issues Summary

### Found Issues

| # | Issue | Test Case | Severity | Description | Resolution |
|---|-------|-----------|----------|-------------|-----------|
| M1 | Precondition 1 Phrasing | AC-22 | Minor | "Known to the tester" ambiguity | FIXED |
| M2 | Conversion Rate Approval | AC-22 | Minor | Conversion sourcing process undefined | DEFERRED |
| M3 | Precondition Count | AC-23 | Minor | Missing precondition 7 on Order type selectability | FIXED |

### Issues Fixed (2)

1. **M1 — AC-22 Precondition 1 Clarity**
   - Old: "User is logged into the trading platform with an account whose base currency is known to the tester."
   - New: "User is logged into the trading platform. The account's base currency must be explicitly confirmed or documented (via UI, configuration, or documentation) prior to test execution."

2. **M3 — AC-23 Precondition 7 Alignment**
   - Added: "**Order type** can be set or confirmed as **Market**; **Sell** side is selectable."

### Issues Deferred (1)

- **M2 — Environment-Approved Conversion Rate Sourcing:** This is a session-level operational concern, not a test structure defect. Recommend documenting the approval source in the session setup documentation (`qa-sessions/web-otc-order-entry/`) rather than embedding it in every test case.

---

## Recommendations

### Action Items

1. **Update AC-22 and AC-23 files with the fixes applied above.**
   - Status: Applied during audit.

2. **Create Session Setup Guide** (for M2 remediation):
   - Document the environment-approved FX conversion rate sources (e.g., Bloomberg, risk engine snapshot, approved calculator).
   - Provide a pre-test verification checklist to confirm conversion source availability.
   - Reference this guide in the session collaboration logs.

3. **Cross-Reference Validation:**
   - Both AC-22 and AC-23 reference `[AC-25]` for precision checks. Confirm that `[AC-25]` test case exists or is in the pipeline.
   - Confirm `[AC-08]` (tiered pricing) and `[AC-07]` (current price rules) are accessible for dependency validation.

4. **Counterfactual Validation Approach:**
   - The counterfactual technique (computing the "wrong" price and proving it differs) is strong. Consider documenting this pattern for future test case design to improve test quality across the suite.

---

## Generated Test Cases Quality Profile

### AC-22: Market Buy Order Value

**Readiness for Execution:** ✅ READY (Minor fixes applied)

**Key Strengths:**
- Comprehensive preconditions with explicit scope boundaries.
- Snapshot-based approach eliminates timing issues.
- Counterfactual validation is strong and rigorous.
- Cross-AC references are thorough.

**Execution Complexity:** Medium (requires conversion rate sourcing, multiple data points to record).

### AC-23: Market Sell Order Value

**Readiness for Execution:** ✅ READY (Minor fixes applied)

**Key Strengths:**
- Proper symmetric mirroring of AC-22 design.
- Clear alignment with bid-based formula for Sell side.
- Counterfactual approach validates Bid (not Ask) is used.
- Preconditions now match AC-22 completeness (after fix).

**Execution Complexity:** Medium (equivalent to AC-22).

---

## Conclusion

Both AC-22 and AC-23 test cases are **well-structured, testable, and ready for execution** after the two minor fixes were applied. The tests demonstrate strong design discipline:

- **Formula validation is rigorous:** Both positive (matches expected) and negative (does not match counterfactual) assertions.
- **Environmental dependencies are flagged:** Conversion rate sourcing is noted as requiring environment approval.
- **Scope is clear:** Explicit inclusion/exclusion of instrument types reduces assumption risk.
- **Preconditions are verifiable:** All preconditions can be checked before test execution.

The tests are ready for manual execution in live or staging environments. No critical or major issues remain unfixed.

---

## Appendix: Fixed Files

The following test case files have been updated with audit fixes and are ready for use:

```
✅ qa-sessions/web-otc-order-entry/case-studies/predictability-analysis/intermediate-states/iteration-11/22-oe-market-buy-order-value.md
✅ qa-sessions/web-otc-order-entry/case-studies/predictability-analysis/intermediate-states/iteration-11/23-oe-market-sell-order-value.md
```

Both files now include the corrected preconditions and improved wording per the audit findings.
