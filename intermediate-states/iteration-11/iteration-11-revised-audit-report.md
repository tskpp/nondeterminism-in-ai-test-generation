# Iteration-11 Revised Methodology Audit Report

**Audit date:** 2026-03-31  
**Methodology:** iteration-11-revised (planning session changes)  
**Scope:** AC-22 (`22-oe-market-buy-order-value.md`), AC-23 (`23-oe-market-sell-order-value.md`)

---

## Executive summary

**Verdict: FAIL — CRITICAL_ISSUES**

Both test cases fail the revised methodology primarily on **Action/Result separation** (computations and comparisons remain in Actions 8–11). Both still use **Ask ≠ Bid** (or equivalent) as a **blocking** precondition. Both **Results** sections still assert the **counterfactual** outcome as a second mandatory result; revised rules require **only the primary assertion** in Results, with counterfactual material optional under **Peculiarities**.

**Major** gaps include **Buy/Sell symmetry** (traceability format, Test Items, precondition wording, styling). **Medium** gaps include **precondition reduction** (seven preconditions with documentation and live-quotes lines), **tester-centric** precondition phrasing, and **N_ask** definitional balance in AC-23 Peculiarities.

Machine-readable detail: `iteration-11-revised-audit.json`.

---

## AC-22 — Detailed findings

### Critical

| Check | Status |
|--------|--------|
| Ask ≠ Bid not a hard precondition | **FAIL** — Precondition 6 requires Ask ≠ Bid for isolating formula legs |
| Actions 1–7 UI-only; no compute/compare in Actions | **FAIL** — Steps 8–9 compute; steps 10–11 compare |
| Results: primary only; counterfactual in Peculiarities | **FAIL** — Second result bullet is counterfactual |
| No when/if in Results | **PASS** |
| No Compute/Compare in Actions 1–7 | **PASS** (steps 8–11 violate overall Actions section) |

### Major

- Traceability structure richer than AC-23 (file paths vs short list).
- Test Items more detailed than AC-23 (Current Ask/Bid called out).
- Precondition wording not mirrored with AC-23 (`Order Entry is open` vs `popup`, obtain vs read).
- Requirement **DX-WEB-097** reference is correct in substance.

### Medium

- Precondition 1: explicit confirm/document base currency — should be trimmed per reduction guidance.
- Precondition 5: “Live quotes are available” — remove from core five.
- Precondition 4: “The tester can obtain” — prefer system-state phrasing.
- `[Valid:]` tags present on buy-side actions; Peculiarities cover Q, LS, M, A, N_ask, C.

**Overall:** CRITICAL_ISSUES  
**Counts:** critical 2, major 1, medium 3 (see JSON for granularity).

---

## AC-23 — Detailed findings

### Critical

| Check | Status |
|--------|--------|
| Ask/Bid differ not a hard precondition | **FAIL** — Precondition 6 requires differing quotes for different notionals |
| Actions free of compute/compare (8–11) | **FAIL** — Same pattern as AC-22 |
| Single primary result; counterfactual optional | **FAIL** — Two result bullets |
| Conditional Results | **PASS** |

### Major

- Traceability abbreviated vs AC-22; Test Items shorter.
- Backticks vs bold for `Sell` / `Market` vs **Buy** / **Market** — presentation drift.
- Step counts align with AC-22 (7 + 11).

### Medium

- Same precondition reduction and “tester can read” issues as paired case.
- Action 4 lacks the explicit `[Valid:]` pattern used in AC-22 step 4.
- Peculiarities emphasize **N_bid**; **N_ask** is used in Action 9 but not defined as prominently as **N_bid** (symmetry / methodology symbol list).

**Overall:** CRITICAL_ISSUES  
**Counts:** critical 2, major 1, medium 4.

---

## Symmetry analysis (Buy vs Sell)

| Dimension | Aligned? |
|-----------|----------|
| Preconditions (count and intent) | Partially — same count, wording diverges |
| Action pattern (11 steps, record/compute/compare layout) | Yes — mirrored ask/bid roles |
| Results pattern | Yes — both use dual assertions (both non-compliant with revision) |
| Field naming (“Order Value”) | Mostly — AC-23 Test Items omit “read-only” and quote labels |
| Traceability | **No** — format and depth differ |

**Notable divergences:** traceability blocks, Test Items, precondition 2/4 wording, markdown conventions for side/type, Options and Peculiarities depth.

---

## Recommendations

### Immediate (apply before sign-off)

1. **Remove** Ask≠Bid (or equivalent) from **blocking** preconditions; optional explanatory note only under Peculiarities.
2. **Reduce** to **five** core preconditions: strip separate “live quotes” line, soften/remove explicit documentation requirement as its own blocker, keep login + base currency need, OE new order, instrument family, Q/LS/M source availability, Market + side.
3. **Move** all computation, conversion, formatting, and comparison from Actions **8–11** into **Results** (expected outcomes / assertions only in Results).
4. **Keep one** primary result per case; move **counterfactual** inequality to **Peculiarities** as optional.
5. **Normalize** AC-23 traceability and Test Items to **mirror** AC-22 (paths, **Order Value** / quote fields, styling).
6. **Align** precondition phrasing (`Order Entry` vs `popup`, obtain vs read) and **define N_ask** in AC-23 Peculiarities with parallel clarity to **N_bid**.

### Deferred

- Clarify whether simultaneous “record” steps stay under Actions or move to a separate observations block if methodology tightens further.
- Optional: harmonize Options ordering and shared **C** / tolerance wording across both files.

---

## Artifact

- **JSON:** `qa-sessions/web-otc-order-entry/case-studies/predictability-analysis/intermediate-states/iteration-11/iteration-11-revised-audit.json`
- **This report:** `iteration-11-revised-audit-report.md`
