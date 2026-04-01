# Never the Same Result Twice in Agentic Descriptive Test Generation

## Executive Summary

It was discovered that when the same test design task is given to AI models multiple times, even with identical instructions, the output differs significantly in structure, terminology, and phrasing. This case study documents how the process was systematized to achieve consistency, identified what causes drift, and learned which constraints actually prevent it.

### Justification

During the first round of descriptive test generation for OTC Order Entry feature, the same requirement logic produced two completely different outputs. The Buy version used "OTC Order Entry" as component name, structured preconditions differently, used specific data partition notes, and formatted peculiarities as variable definitions. The Sell version used "Order Entry", reorganized preconditions, omitted data partitions, and wrote peculiarities as narrative text. Both files claimed to verify the same logical behavior—only the side changed (Buy vs. Sell).

This raised a critical question: can we reliably use AI models for test generation when the output quality depends on context window state, model choice, and isolated execution?

### Goal

Determine whether consistent, high-quality test generation is achievable through systematic rules, constraints, and audit-driven correction. If yes, document the methodology. If no, identify the irreducible variance and its causes.

### Hypotheses

1. **Hypothesis 1:** Non-determinism stems from loose generator instructions. Strict syntactic rules in the generation prompt will reduce drift.
2. **Hypothesis 2:** Isolation causes asymmetry. If agents are forced to read existing tests before generating new ones, they will calibrate their style and maintain consistency.
3. **Hypothesis 3:** Post-generation auditing can never fully fix drift because it operates reactively. Embedding audit rules into the generation phase (pre-generation) will prevent most issues from appearing.
4. **Hypothesis 4:** Different AI models (fast vs. capable vs. thinking) will produce fundamentally different outputs even under identical constraints, requiring model-specific tuning.
5. **Hypothesis 5:** An external auditor reading all generated files simultaneously can catch and correct asymmetries better than any single generation-time constraint.

### Faced Impediments

1. **Premature Finality:** Early attempts classified intermediate outputs as "target state" without explicit user approval, violating methodology rigor.
2. **Style-Sync Fragility:** Forcing generators to read adjacent files assumes tests are always generated sequentially and that context windows preserve file references. This breaks in parallel generation scenarios.
3. **Lazy References ("Black Boxes"):** Generated tests referenced external sources and formulas without stating them explicitly, making tests unexecutable without external lookup.
4. **Paired-Test Asymmetry:** Buy and Sell test cases diverged despite identical logical coverage. Field names differed (`Stop` vs. `Order Price`), preconditions used different phrasing, peculiarities included different variable definitions.
5. **Cognitive Actions in Test Steps:** Generators embedded mental calculations ("Compute default_distance", "Compare values") directly in Actions sections. Per audit rules, Actions must contain only user interactions; computation belongs in Results/Peculiarities.
6. **Traceability Contradiction:** Methodology rules said to inherit requirement IDs across paired tests, but the source test list explicitly excluded IDs from one variant. Agents correctly trusted the source over methodology, exposing a broken assumption.
7. **Redundant Preconditions:** Preconditions bloated with unnecessary constraints (e.g., "Ask ≠ Bid required") unrelated to the actual test goal, artificially limiting test applicability.

### Key Results

1. **Strict Rule Sets Enable Consistency:** After implementing 7 explicit audit rules (Anti-Cognitive, Anti-Conditional Results, Data Partition Syntax, Imperative Actions, etc.), parallel generation by different models produced symmetrical outputs 85%+ of the time.

2. **Post-Generation Auditing is Essential:** Even with tight rules, isolated parallel agents drift. A single auditor reading both generated files simultaneously can detect and correct all asymmetries (field name divergence, precondition phrasing, options alignment) with 100% accuracy.

3. **Context-Rich Methodology Beats Model Selection:** Both fast models (Haiku) and capable models (Gemini) succeeded under the same strict rules when those rules provided rich context about system state, field naming, precondition phrasing, and audit criteria. Rather than models "cherry-picking" features or optimizing for performance (max-moding), they produced consistent outputs when given explicit, digitized knowledge about expected structure, terminology, and constraints. This demonstrates that providing AI agents with comprehensive domain context and explicit system state documentation yields better results than hoping models will make correct assumptions. The quality difference between models was not significant when proper context was available; model choice became secondary to methodology maturity.

4. **Sequential Generation with Golden Example is Fragile:** Forcing agents to read prior test cases worked in controlled lab settings but is not reliable in production. A single auditor pass is more robust.

5. **Source Traceability Must Be Validated Upstream:** Inconsistencies in the source test list (e.g., missing requirement ID on one variant) cannot be silently fixed by generators. Instead, auditors must flag the source itself as inconsistent and fix generation with the missed items in source or request user input.

6. **Precondition Purity Matters:** Tests written in "system state" language (e.g., "platform exposes X value") rather than "tester capability" language (e.g., "tester can read X value") are more resilient and less ambiguous.

7. **Final Outputs Benefit from Iteration:** After 11 iterations of generation and correction, test quality was highest. Early iterations (1–3) showed structural issues; mid-range iterations (4–8) had symmetry and phrasing drift; late iterations (9–11) showed near-perfect consistency after rules matured.

### Key Conclusions

1. **AI-Generated Tests Are Reproducible With Discipline:** Contrary to the initial observation ("never the same result twice"), consistent high-quality test generation is achievable if strict constraints, explicit audit frameworks, and single-auditor review are enforced systematically. The variance is not inherent to AI; it results from loose methodology.

2. **Parallel Generation Requires Auditing:** Generating tests in parallel (for speed) introduces unavoidable drift in field naming, precondition phrasing, and step ordering. Choosing parallel generation must lead for a single-auditor correction pass. Choosing sequential generation must implement "golden example" passing, which is context-sensitive and fragile.

3. **Methodology Maturity Matters More Than Model Selection:** The 11 iterations show that refining rules and constraints had far greater impact on output quality than switching between different models and seeking for the most suitable one for the particular test set. Investment in methodology design pays higher returns than model tuning.

4. **Prevention Beats Correction:** Early iterations relied on post-generation auditing (expensive, reactive). Late iterations embedded audit rules into generation prompts (cheaper, proactive). Embedding rules directly into generation instructions reduced auditor corrections by 60%.

5. **Source Integrity is a Prerequisite:** Test generation quality is bounded by source data quality. Inconsistent test lists, vague requirement IDs, or missing component definitions cascade into test asymmetry.

6. **Explicit Rules Beat Implicit Assumptions:** Tests fail when generators make unstated assumptions (e.g., "preconditions can include tester capability language" or "counterfactual validation is always required"). Explicit negative rules ("never use tester-knowledge language") and explicit positive examples ("system state looks like this") prevent failures.

---

## Case Study Stages

### Stage 1: Initial Observation & Problem Discovery

**What happened:** Two test cases (`AC-10` Buy and `AC-11` Sell) were generated in separate sessions to verify the same functional logic. Despite near-identical requirements, the outputs diverged in component naming, precondition organization, data partition syntax, field label naming, and peculiarities structure.

**Issues caught:**
- Inconsistent component names ("OTC Order Entry" vs. "Order Entry")
- Preconditions reordered and rephrased
- Different data partition syntax (inline vs. missing)
- Different field terminology (`Order Price` vs. implicit)
- Peculiarities as variable definitions vs. narrative notes
- Requirement ID references dropped in one variant

**Files:** See `/initial-state/` folder for original divergent outputs

---

### Stage 2: Root Cause & Methodology Design (Iterations 1–3)

**What happened:** We diagnosed the causes—loose generator instructions, session isolation, dynamic variable generation, lazy references—and designed initial fixes: Strict Golden Template, Centralized Lexicon, Style-Sync, and Guided Intelligence Rules.

**Corrections applied:**
- Skill created with 7 explicit rules (imperative actions, no conditionals in results, data partition syntax, etc.)
- Attempted "Style-Sync" (read adjacent file before generating); user feedback rejected this as fragile
- Implemented strict audit framework and began structured iteration naming (`iteration-1`, `iteration-2`, etc.)
- Added Anti-Rushing guard to global methodology (no premature "Final" or "Target" declarations)

**Issues caught during iterations:**
- Iteration 1: Premature finality, loss of traceability
- Iteration 2: Excessive backticks, dropped requirement IDs, missing peculiarities
- Iteration 3: Over-formatting in data partitions, slight asymmetries in parallel generation

**Files:** See `/intermediate-states/iteration-1/` through `/intermediate-states/iteration-3/` folders

---

### Stage 3: Audit-Driven Corrections & Traceability Fixes (Iterations 4–8)

**What happened:** We applied formal auditing using a structured 7-rule audit framework. Iterations 4–6 focused on fixing logical errors (conditionals in results, cognitive actions), enforcing symmetry between Buy/Sell variants, and restoring traceability. Iterations 7–8 tested the mixed-model scenario (Haiku + Gemini) and corrected structural asymmetries.

**Corrections applied:**
- Iteration 4: Removed conditional logic from Results, restructured Peculiarities, enforced exact symmetry between Buy and Sell
- Iteration 5: Tested parallel generation with mixed models (Default for AC-10, Fast for AC-11); deliberately introduced variance
- Iteration 6: Single auditor corrected all asymmetries (data partitions, field naming, punctuation)
- Iterations 7–8: Tested additional test pairs (AC-22/AC-23 Market Buy/Sell); auditor corrected for counterfactual removal and precondition purity

**Issues caught:**
- Iteration 4 Audit: Conditional results ("when live bid..."), functional checks in Peculiarities instead of pure formulas
- Iteration 5 Audit: Data partition missing on one variant, field naming divergence (`Stop` vs. `Order Price`)
- Iteration 8 Audit: Tester-knowledge language in preconditions ("tester can read"), counterfactual checks in parallel cases

**Files:** See `/intermediate-states/iteration-4/` through `/intermediate-states/iteration-8/` folders; audit reports in `/logs/iteration-*.json`

---

### Stage 4: Model Comparison & Rule Validation (Iterations 9–10)

**What happened:** We ran parallel generation using fast model (Haiku) and default model (Gemini) under identical strict rules to validate whether model selection was a primary variance driver. A comprehensive auditor then compared both iterations and synthesized improved versions.

**Results:**
- **Haiku (Fast Model):** Perfect symmetry, consistent terminology (`Stop` price field), explicit formulas, no conditionals. **Weakness:** Cognitive steps in Actions sections ("Compute default_distance" was in Actions, not Results).
- **Gemini (Default Model):** Clean Action/Result separation, system-state preconditions (not tester-knowledge). **Weakness:** Asymmetry in field naming (Buy `Stop`, Sell `Order Price`), step ordering drift, precondition phrasing inconsistency in the Sell variant.

**Audit findings:** Both iterations had strengths and weaknesses in different dimensions. No single model dominated. The auditor synthesized best-practice versions combining Haiku's symmetry and terminology with Gemini's action-result separation.

**Key Insight:** Model choice is secondary to rule explicitness. Both models succeeded under strict constraints; both drifted under loose ones.

**Files:** See `/intermediate-states/iteration-9-haiku/`, `/intermediate-states/iteration-9-gemini/`, and `/intermediate-states/iteration-10-haiku-thinking-audit/` folders; detailed comparison in `comparison-analysis.md`

---

### Stage 5: Methodology Consolidation & Final Generation (Iteration 11)

**What happened:** Following the success of Iteration 10, all insights from the case study were consolidated into the global methodology and related audit frameworks). A new planning session approved enhancements: test-type selection, counterfactual best-practices, precondition reduction, and formalized 7-rule audit framework.

**Corrections applied:**
- Removed unnecessary preconditions (Ask ≠ Bid, redundant "Live quotes available", ambiguous "Approved configuration source")
- Simplified counterfactual validation to 3 clear rules (use when wrong input can produce correct output, never block generation, integrate as explanation not separate result)
- Moved computation/comparison logic from Actions to Results (Action/Result separation enforcement)
- Generalized paired-test approach for any variant (Buy/Sell, +/-, Synchronized/Asynchronous)

**Files:** See `/intermediate-states/iteration-11/` folder and associated revision reports.

---

### Stage 6: Production-Ready Generation (Final State)

**What happened:** Using all refined rules and constraints, test cases were regenerated in two rounds using different models. Both the fast model (Haiku) and the default model (Gemini) were run independently to generate AC-10 (Buy) and AC-11 (Sell) test cases. Results from both model runs were nearly identical in structure, logical completeness, and accuracy, differing only in minor styling, wording choices, and formulation nuances.

**Quality metrics:**
- Audit pass rate: 100% (zero critical or major findings in both model runs)
- Symmetry score: 100% (identical preconditions, actions, results between Buy and Sell variants except for directional changes)
- Traceability: 100% (both variants inherit identical requirement IDs)
- Anti-Cognitive: 100% (zero computation steps in Actions)
- Cross-model equivalence: 100% (Haiku and Gemini outputs considered functionally equivalent; differences limited to phrasing and formatting)

**Key observation:** The similarity between Haiku and Gemini outputs demonstrated that when methodology is mature and context is rich, model selection becomes a styling decision rather than a functional one. Both models produced test cases that pass all audits and verify the same requirements using the same logical approach.

**Files:** See `/final-state/` folder for the production-ready test cases. This folder contains results from final generation runs showing consistency across multiple model executions.

---

## Summary

This case study transformed the perception that AI test generation is inherently non-deterministic ("never the same result twice") into a practical methodology where consistency is achievable through discipline. The 11-iteration journey from divergent outputs to production-ready symmetry demonstrates that:

- **Variance is not inherent to AI; it stems from loose methodology.**
- **Strict rule sets + external auditing can enforce consistency at scale.**
- **Methodology design has higher ROI than model selection tuning.**
- **Source data integrity is a prerequisite for test quality.**

---

## Author

Stanislav Lobov

---

## References

- **Initial State (Divergent Outputs):** `/initial-state/`
- **Intermediate States (Iteration 1–11):** `/intermediate-states/iteration-[N]/`
- **Audit Logs:** `/logs/iteration-*.json`
- **Final State (Production-Ready):** `/final-state/`
- **Case Study Log (Detailed Iteration Notes):** `case-study-log.md`
- **Deferred Work:** `LEFTOVERS.md`