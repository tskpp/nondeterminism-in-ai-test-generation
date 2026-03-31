# Iteration 9 (Haiku vs Gemini) — Comparative Audit Analysis

## Executive Summary

**Haiku** delivers strong **Buy/Sell symmetry** and consistent **`Stop` price** terminology but **fails Rule 3** by placing **compute/compare** steps in **Actions**. **Gemini** fixes **Rule 3** on **Buy** while **Sell** regresses on **terminology**, **preconditions**, and **pair symmetry**; overall the iterations are best treated as a **tie** and merged into one improved pair.

---

## Strengths and Weaknesses (Side by Side)

| Dimension | 9-haiku | 9-gemini |
|-----------|---------|----------|
| **Rule 1 — Atomicity** | Single flow: default value + quote freeze; same scope as Gemini | Same bundled observable checks | Tie |
| **Rule 2 — Results phrasing** | Present tense, no should/will/if | Present tense | Tie |
| **Rule 3 — Action/Result separation** | **Fail**: Compute, compare in Actions | **Buy pass**; Sell pass on interactions | **Gemini better on Buy**; Haiku fails both |
| **Rule 4 — Preconditions / UI** | Tester/config “known” wording | Buy: system-oriented; **Sell: “known to tester”** | **Gemini Buy** best; **Sell** worst |
| **Rule 5 — Terminology** | **`Stop` price** both sides | **Buy `Stop`**, **Sell `Order Price`** | **Haiku** |
| **Rule 6 — Anti-counterfactual** | No opposite-scenario math | No opposite-scenario math | Tie |
| **Rule 7 — Symmetry** | **Strong** mirror Buy/Sell | **Weak** Sell vs Buy | **Haiku** |

---

## Detailed Findings by Rule

### 9-haiku — `10-oe-default-stop-price-buy.md` / `11-oe-default-stop-price-sell.md`

| Rule | Finding |
|------|---------|
| **1** | One logical acceptance: correct default from formula + unchanged after live quote updates. Acceptable as one case; not split here. |
| **2** | Results use declarative present tense. Compliant. |
| **3** | **Critical**: Actions 2, 3, 7 perform calculation and comparison. Non-interaction steps must move to Results. |
| **4** | **Major**: Precondition 3 frames parameters as “known from … UI” rather than strictly “displayed/exposed by the system.” |
| **5** | Consistent `Stop` price field and traceability IDs. Compliant. |
| **6** | No verification of the opposite side’s formula inside Buy/Sell. Compliant. |
| **7** | Buy/Sell aligned except for direction and Ask/Bid. Compliant. |

### 9-gemini — `10-oe-default-stop-price-buy.md`

| Rule | Finding |
|------|---------|
| **1** | Same dual outcome as Haiku; acceptable for this AC. |
| **2** | Compliant. |
| **3** | Actions are interactions and recordings only. Compliant. |
| **4** | Preconditions describe streaming quotes and exposed parameters. Compliant. Minor: Summary style differs from Object - Action shorthand. |
| **5** | Uses `Stop` price field. Compliant. |
| **6** | Compliant. |
| **7** | Must be judged with Sell — see Sell. |

### 9-gemini — `11-oe-default-stop-price-sell.md`

| Rule | Finding |
|------|---------|
| **1** | Same as Buy. |
| **2** | Compliant. |
| **3** | No compute in Actions. Compliant. |
| **4** | **Major**: “Known to the tester” in Preconditions. Minor: vague “standard marginal FX or CFD” in Valid clause. |
| **5** | **Major**: **Order Price** vs **Stop** price on Buy — inconsistent domain labeling. |
| **6** | Compliant. |
| **7** | **Major**: Asymmetry vs Gemini Buy (field name, step ordering narrative, Options for rounding only on Sell). |

---

## Recommendation for Future Generations

1. **Template paired ACs from one skeleton** — Generate Buy and Sell from the same step list, substituting only Buy/Sell, Ask/Bid, and plus/minus so **Rule 7** holds by construction.
2. **Lint Actions for forbidden verbs** — Reject or flag **Compute**, **Calculate**, **Compare** (to expected), **Verify** inside **Actions**; keep them in **Results** or **Peculiarities** (definitions only).
3. **Precondition checklist** — Require “system displays / platform exposes / feed is active” and disallow “tester knows / tester can read” unless rephrased as UI or API observability.
4. **Lock field labels** — Pull control names from a single glossary (e.g. always **Stop price** for this flow) so model drift does not rename fields between paired files.
5. **Snapshot semantics** — State explicitly whether the reference quote is **before** side/type selection or **at** first population of the Stop field; use the same rule for Buy and Sell.

---

## Output Artifacts

| Artifact | Path |
|----------|------|
| Audit JSON | `intermediate-states/iteration-9-audit-package/audit-report.json` |
| Improved Buy | `intermediate-states/iteration-9-audit-package/10-oe-default-stop-price-buy.md` |
| Improved Sell | `intermediate-states/iteration-9-audit-package/11-oe-default-stop-price-sell.md` |
