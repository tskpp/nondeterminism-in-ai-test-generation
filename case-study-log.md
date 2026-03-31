# Case Study: Predictability Analysis and Consistency Enforcement

## 1. User Questions and Observations

The user observed a severe discrepancy in formatting, structure, and phrasing between two descriptive test cases (`10-oe-default-stop-price-buy.md` and `11-oe-default-stop-price-sell.md`) despite them verifying nearly identical logic (Buy vs. Sell).

Specific inconsistencies noted by the user:
* **2.0:** Different Summary formatting ("Calculate default Buy Stop price" vs. "Default stop populated for Sell").
* **2.1:** Different header styles (H1 `# Traceability` vs inline `**Traceability:**`).
* **2.2:** Lack of explicit requirement ID references in case 11.
* **2.3:** Different Component names ("OTC Order Entry" vs "Order Entry").
* **2.4:** Different Test Items ("Order Entry UI, Default Price Calculation" vs "Order Entry Form").
* **2.5:** Different Preconditions lists.
* **2.6:** Misalignment between Actions and Preconditions (Opening the popup was a precondition in case 10, but an action in case 11).
* **2.7:** Different syntax for highlighting objects (single quotes `'Buy'` in 10 vs backticks `` `Sell` `` in 11).
* **2.8:** Missing explicit `[Valid: ...]` data partition note in case 11.
* **2.9:** Different phrase composition for the exact same logic (Action 3 vs Action 4).
* **2.10:** Results formatted as a structured bulleted list in 10, but as a narrative paragraph in 11.
* **2.11:** Peculiarities section had a note in 10, but none in 11.

## 2. Root Cause Analysis

This demonstrates a classic limitation of Large Language Models: **Non-determinism and Context Drift**.

1. **Loose Generator Instructions:** The `qa-descriptive-test-generation` skill dictates which sections to include (Summary, Preconditions, etc.) but doesn't provide strict syntactic rules on *how* to format them (e.g., bullet lists vs paragraphs, quotes vs backticks).
2. **Session Isolation:** Case 10 was generated in session 1, where the agent improvised a specific style. Case 11 was generated in session 2 with a completely fresh context window. The agent didn't look at Case 10 to calibrate its style, causing it to improvise a new style entirely from scratch.
3. **Dynamic Variable Generation:** Fields like `Component` and `Test Items` were deduced dynamically by the LLM based on the text of the requirement, rather than being strictly inherited from a centralized, pre-defined lexicon.
4. **Reactive Auditing instead of Proactive Enforcement:** The agent relies on a strict Auditor (`test-case-auditor.md`) to catch mistakes *after* they are written, rather than explicitly passing the Auditor's rules to the Generator *before* it begins.

## 3. Plan of Corrections

To fix the "never the same result twice" issue and move to a highly systemic framework:
1. **Implement a Strict Golden Template:** Create a standardized markdown template that explicitly dictates the formatting of every single field.
2. **Centralize the Test Lexicon:** Add a metadata block to the `test-list.md` to define global variables (like `Component`, `Test Items`, and standard `Preconditions`) so the agent copies them verbatim instead of dynamically generating them.
3. **Implement "Style-Sync" (Context Calibration):** Update the generation methodology to force the agent to read an existing base test case before generating new ones in the same suite.
4. **Shift Left the Auditor Rules:** Embed the core auditor rules directly into the test generation skill so the generator follows them correctly on the first pass.

## 4. Execution State (Iteration 1 - Initial Adjustment Attempt)

The initial adjustment attempted to solve the problem using a "Style-Sync" (Phase 0.5) approach.
- **Action:** Created `mock-test-list.md` with a central lexicon in frontmatter.
- **Action:** Created `mock-skill.md` with a strict template and a rule to "read the first numerically ordered file" to copy its style.
- **Action:** Regenerated AC-10 and AC-11 in a `target-state` folder (later renamed to `intermediate-states/iteration-1`).

**User Feedback on Iteration 1:**
1. **Premature Finality:** The agent rushed to put files in a `target-state` folder without user approval, demonstrating "amateur performance".
2. **Loss of Traceability:** The agent removed explicit references to the requirements in the generated files during the "Style-Sync" generation.
3. **Flawed Concept ("dumb copying"):** The user correctly pointed out that "Style-Sync" (forcing the agent to read an adjacent file) is fragile. In real scenarios, test cases might not be generated sequentially, and the context file might not be in the context window.

## 5. Course Correction (Iteration 2)

Based on the feedback, the methodology and agentic rules were updated:

### 5.1 Removing Premature Finality
- **Action:** Deleted the `target-state` folder.
- **Action:** Restructured output directories into `intermediate-states/iteration-[N]/` to track evolution.
- **Agentic Behavior Update:** Added an **"Anti-Rushing & State Assumption"** guard to `.cursor/rules/agentic-behavior.mdc` (Section 5). This strictly forbids the agent from declaring a workflow as "Final" or "Target State" without explicit human authorization.

### 5.2 Restoring Traceability
- **Action:** Updated the markdown template in `mock-skill.md` to explicitly require both the Test List ID and the Requirement ID.

### 5.3 Rethinking "Style-Sync" into "Guided Intelligence"
- **Action:** Removed the "read adjacent file" rule from `mock-skill.md`.
- **Action:** Replaced it with a **Linguistic & Syntactic Rulebook** explicitly dictating formatting intelligence directly in the prompt.
  - *Rule 1:* Always wrap UI elements, buttons, tabs, and sides in backticks.
  - *Rule 2:* Actions must strictly start with an imperative verb (*Open*, *Select*).
  - *Rule 3:* Multiple results MUST be formatted as a bulleted list.
  - *Rule 4:* Inline data partitions must strictly follow `` `[Valid/Invalid: explicit value]` `` at the end of the action step.

### 5.4 Iteration 2 Generation Results
- **Action:** Regenerated AC-10 and AC-11 using the Guided Intelligence approach in `intermediate-states/iteration-2/`.
- **Result:** The generated files (`10-oe-default-stop-price-buy.md` and `11-oe-default-stop-price-sell.md`) were more structured but introduced new issues: excessive backticks (e.g. `` `[Valid...]` ``), dropped Requirement ID for the second case, and dropped Peculiarities for the second case.

## 6. Execution State (Iteration 3 - Refined Guided Intelligence & Anti-Lazy References)

Based on feedback from Iteration 2, the Guided Intelligence rules in `mock-skill.md` required fine-tuning to prevent over-formatting, ensure inheritance across paired cases, and eliminate "lazy references" to documentation.

### 6.1 Fixing Formatting Overkill
- **Action:** Updated Rule 1 to explicitly limit backticks to UI elements only (no double backticks).
- **Action:** Updated Rule 4 to explicitly forbid wrapping the `[Valid: ...]` partition note in backticks.

### 6.2 Enforcing Paired Inheritance
- **Action:** Added Rule 5 (Traceability Inheritance): Explicitly dictates that paired test cases (Buy/Sell variants) must inherit the exact same `[Req-ID]` from the test list.
- **Action:** Added Rule 6 (Peculiarities Preservation): Explicitly dictates that if a Peculiarity applies to the functional logic of a paired test case, it must be identical across both cases.

### 6.3 Eliminating "Lazy References" (The "No Black Boxes" Rule)
- **Observation:** The generated peculiarity referenced "Confluence default-distance rules", which is senseless and unactionable without the external link. The formula was actually defined in the requirements.
- **Action:** Updated `mock-test-list.md` to remove the vague reference in AC-10 and replaced it with the explicit calculation formula.
- **Action:** Added Rule 7 to `mock-skill.md` enforcing Peculiarity Clarity (Anti-Lazy References) to forbid vague pointers.
- **Action:** Updated global methodology (`.cursor/rules/agentic-behavior.mdc`) with a new guard: **Anti-Lazy References (The "No Black Boxes" Rule)**.

### 6.4 Iteration 3 Generation via Parallel Sub-agents
- **Action:** Used the Task tool to spawn two parallel sub-agents (both using the default model) to generate AC-10 and AC-11 simultaneously into `intermediate-states/iteration-3/`. 
- **Result:**
  - **Traceability:** Both cases successfully inherited `DX-WEB-095`, `DX-WEB-086`, fixing the drop issue from Iteration 2.
  - **Formatting:** The over-formatting was fixed. `[Valid...]` is no longer in backticks, and only UI elements are in single backticks.
  - **Anti-Lazy:** The black-box Confluence reference was successfully replaced with the explicit mathematical formula in both cases.
  - **Observation on Parallelism:** Because the agents ran in parallel isolated contexts, they both included the exact formula, but formatted it slightly differently (Agent A broke it down into multi-line variable definitions, Agent B kept it as a single inline bullet). This highlights that while strict rules guarantee *content completeness*, achieving exact character-for-character symmetry in free-form text blocks requires either sequential generation or even stricter markdown schema injection.

## 7. Execution State (Iteration 4 - Remediation via Strict Audit)

After generating Iteration 3, the user requested an explicit audit using **both** the standard `test-case-auditor.md` and the case-study specific `mock-skill.md` rules.

### 7.1 Audit Findings (Iteration 3)
- The audit report was generated and saved to: `logs/iteration-3-audit.json`.
- The audit failed due to several major and critical issues across both cases, demonstrating that even with the refined generator rules, the parallel sub-agents still produced policy violations.
- **Critical Issues (Both files):** Violated Rule 2 (Unambiguous Results). Both files used "when" clauses in the Results section ("stays fixed when live bid..."), which is strictly forbidden as conditional logic in results.
- **Major Issues (Buy case):** The `Peculiarities` section contained step-like comparison execution guidance instead of pure formulas/notes.
- **Major Issues (Sell case):** The `Peculiarities` section repeated a functional outcome ("does not re-tick"), which belongs in Results. It also failed to mirror the Buy case's structured variables (A, B, D, P, I) and explicit data partitions.

### 7.2 Applying Corrections (Iteration 4)
Based on the audit report, files in `intermediate-states/iteration-4/` were created with the following fixes:

1. **Eliminated Conditionals in Results:** Removed the "when" clauses from the Results section in both files. Changed to unconditional present-tense statements (e.g., "The `Order Price` field value remains unchanged from the step-4 recording after live quotes change in step 5.").
2. **Restructured Peculiarities for Purity:** Removed the functional checks and execution guidance from the `Peculiarities` section in both files. Moved the comparison step explicitly into the `Actions` section (as Step 6).
3. **Enforced Symmetry (Sell mirroring Buy):** 
   - Replicated the precise Preconditions (D, P, I definition) from the Buy case into the Sell case.
   - Replicated the exact structured format of the `Peculiarities` section (using the same A, B, D, P, I variables and explicit formula breakdown) into the Sell case, adjusting only the final operation to subtraction (B - default_distance) for a Sell order.
   - Applied explicit, quantified `[Valid: ...]` data partitions to Action 2 in the Sell case, matching the Buy case.
4. **Cleaned up UI Highlighting:** Ensured consistent backticks across both files for specific UI labels like `Order Price` and `Order type`.
5. **Removed Shorthand References:** Removed the "per new-order behavior" shorthand from the `[Valid...]` partition in the Buy case.

## 8. Execution State (Iteration 5 - Testing Mixed Agents with New Rules)

Following the analysis of why Iteration 4 was successful (due to a single auditor explicitly enforcing symmetry across files), the global methodology and the case study mockups were updated to systemize this approach:
- **Phase 0.1 (Generation Planning):** Require a small initial batch to establish a "golden example".
- **Rule 7 (Symmetry and Cross-Validation):** Enforce the auditor to strictly cross-reference cases in the same batch.
- **Anti-Mass-Generation Guard:** Added to global `agentic-behavior.mdc` to prevent generating massive unverified test suites.

To test these new rules, we ran **Iteration 5**:
- We explicitly spawned **two different subagents** running in parallel to generate the test cases based on the new `mock-skill.md`.
- **Subagent A** ran the **Default model** to generate AC-10 (Buy).
- **Subagent B** ran the **Fast model** to generate AC-11 (Sell).
- *Goal:* Deliberately introduce variance between the two files to see if the mixed models drift, setting up a test for the single auditor.

### 8.1 Iteration 5 Results
Both files successfully implemented the core Phase 0.5 rules (imperative actions, no "per Confluence" lazy references, correct UI backticks). However, due to the different models running in isolation, slight symmetry drifts occurred (e.g., Subagent B used `Select 'Sell'` instead of `Select the 'Sell' side`, and missed the `[Valid: standard marginal FX or CFD...]` partition on Action 1 that Subagent A included).

## 9. Execution State (Iteration 6 - Remediation via Single Auditor)

To fix the deliberate drift introduced in Iteration 5, we spawned a **Single Auditor** subagent.

### 9.1 Single Auditor Handoff
- The single auditor read both files from Iteration 5 simultaneously, applying the newly minted **Rule 7: Symmetry and Cross-Validation**.
- **Audit Findings:** The auditor successfully caught the symmetry gap. It flagged the missing data partition on Action 1 in the Sell case, the slight mismatch in the wording of "Select Sell", and a punctuation difference in Action 3. 
- The auditor generated a JSON report to `logs/iteration-5-audit.json` with a status of `needs_improvement`.

### 9.2 Applying Corrections (Iteration 6)
The auditor autonomously applied the required fixes to synchronize the files and wrote the final, completely symmetrical output to `intermediate-states/iteration-6/`:
- Shared Action 1 (`Select the Buy/Sell side` + exact same instrument partition).
- Action 3 punctuation strictly aligned.
- Results unconditionally matching and strictly separated from timing triggers.

## 10. Root Cause Analysis: Missing `DX-WEB-086` in Iteration 6

The user noticed that in Iteration 6, `11-oe-default-stop-price-sell.md` was missing the `DX-WEB-086` requirement ID in its traceability block, despite our new rules. The file was lost due to an external reset/cleanup, but upon re-creation and review, we identified the exact cause.

### 10.1 The Contradiction
The root cause of missing `DX-WEB-086` lies in a direct contradiction between the explicit instructions in the source `test-list.md` and the implicit methodological rule we added ("Traceability Inheritance").

1. **Explicit Source Mapping:** In the `test-list.md`, the acceptance criteria were defined as:
   * `[AC-10]` Maps to: `DX-WEB-095`, `DX-WEB-086`
   * `[AC-11]` Maps to: `DX-WEB-095` (It explicitly did *not* include `DX-WEB-086`).
2. **The Rule:** We added "Rule 5: Traceability Inheritance" to `mock-skill.md`, stating that paired cases must inherit the exact same Requirement IDs.
3. **Agent Behavior:** When the generator and auditor ran, they were forced to choose between blindly following the explicit, hardcoded mapping in the source document (`test-list.md`) or overriding it based on the stylistic inheritance rule.

Both agents correctly trusted the explicit mapping in the source document over the methodological inheritance rule. From the agents' perspective, adding `DX-WEB-086` to AC-11 when it was missing from the test list would be an assumption or hallucination.

### 10.2 The Solution
We cannot instruct generators to silently copy requirements that aren't in the source list. This leads to dangerous assumptions. Instead, we must push the validation upstream.

**Adjustment:** We updated the generator and auditor skills so that "Traceability Inheritance" is explicitly enforced as a strict check against the source `test-list.md`. If the source list itself is asymmetric (e.g., missing a requirement like DX-WEB-086 on AC-11 that is present on AC-10), the auditor MUST flag the `test-list.md` itself as flawed and halt, rather than silently dropping the requirement from the generated test case.

## 11. Execution State (Iteration 7 - Global Methodology & Traceability Symmetry)

To address the traceability drift observed between test cases (e.g., `[AC-22]` mapping to `DX-WEB-097 / Order Value section` while `[AC-23]` mapped only to `Order Value section`), we implemented a new rule in the global methodology: **Traceability Symmetry**.

### 11.1 Updates
- Added centralized frontmatter (`Global Component Name`, etc.) to the global `.cursor/templates/qa-test-list.md.template`.
- Added the **Traceability Symmetry Rule** to `.cursor/skills/qa-test-list-generation/SKILL.md` to prevent "lazy abbreviation" when splitting paired test cases.
- Manually fixed `test-list.md` to explicitly state `DX-WEB-097` for `[AC-23]`.

### 11.2 Iteration 7 Generation Results
We spawned two isolated subagents (Default model for AC-22, Fast model for AC-23) to generate the test cases based on the global methodology.
- **Result:** Both subagents successfully generated the cases in `intermediate-states/iteration-7/`. The Traceability blocks were perfectly aligned (`[AC-22]` and `[AC-23]`, both mapping to `DX-WEB-097`). The Guided Intelligence rules (imperative actions, no "when" in Results, correct UI backticks) were mostly followed. However, due to model isolation, structural asymmetries appeared (e.g., Buy included a bid counterfactual step, Sell did not; Sell checked display precision in Results, Buy pointed it to Options).

## 12. Execution State (Iteration 8 - Remediation via Single Auditor)

To correct the asymmetries from Iteration 7, a Single Auditor subagent was run.

### 12.1 Audit Findings (Iteration 7)
- Caught major symmetry gaps: Buy included a bid counterfactual, Sell did not. Sell's Results checked display precision (AC-25 territory) while Buy correctly relegated it to Options.

### 12.2 Applying Corrections (Iteration 8)
The auditor generated perfectly symmetrical versions in `intermediate-states/iteration-8/`:
- Synchronized Preconditions (all 6 items identical).
- Synchronized Actions (11-step pattern with primary side quote, opposite quote, expected notional, counterfactual notional, and comparisons).
- Mirrored Results and Options perfectly.

## 13. Execution State (Iteration 9 - Priority Attribute Removal)

### 13.1 Root Cause of Priority Usage
The user explicitly ordered the removal of the `# Priority` attribute from all tests. However, the `test-case-auditor` subagent continually enforced its presence. The root cause was that the `Priority` requirement was deeply embedded in multiple core methodology files:
- `.cursor/skills/test-master/references/test-case-auditor.md` (and its `cursor-methodology` counterpart) listed it as a Mandatory Frontmatter field.
- The auditor's strict adherence to its instructions caused it to "correct" the lack of Priority by inserting it into Iteration 8's outputs.

### 13.2 Remediation
1. Removed `Priority` from the Structural Requirements block in `.cursor/skills/test-master/references/test-case-auditor.md` (and the submodule counterpart).
2. Removed `# Priority` from the generated test cases in `intermediate-states/iteration-8/22-oe-market-buy-order-value.md` and `23-oe-market-sell-order-value.md`.

## 14. Execution State (Iteration 10 - Removing Redundant Cognition and Counterfactuals)

### 14.1 Audit Findings (Iteration 8 Review)
The user requested an analysis of Iteration 8 for redundancy. We identified several critical methodological flaws:
1. **Cognitive Actions:** The `Actions` sections contained steps like "Compute N_ask" and "Compare the recorded Order Value". These are mental math steps, not physical interactions with the system.
2. **Counterfactual Checking:** Both tests computed the opposite side's formula (e.g., calculating Bid logic in a Buy test) just to prove the final outcome differed from it. This violates Atomicity because it cross-pollinates logic that is already covered by the opposing test case (`[AC-23]`).
3. **Tester-Centric Preconditions:** Preconditions specified what "the tester can read" rather than defining the explicit system state.

### 14.2 Root Cause Analysis
The methodology's Action/Result separation rules and Atomicity rules were not explicit enough regarding mathematical formulas and scenario isolation. The generator over-engineered the tests, treating them as isolated mathematical proofs instead of atomic verifications of expected positive paths. It treated the `Actions` list as a human's mental worksheet rather than a sequence of strict system inputs.

### 14.3 Methodology Remediation
We updated the core skills instead of manually fixing the generated tests, ensuring the agents stop making these assumptions globally:
1. **Anti-Cognitive Rule:** Updated `.cursor/skills/qa-descriptive-test-generation/SKILL.md` (Phase 0.5, Rule 2) and the `test-case-auditor.md` (Rule 3) to explicitly ban cognitive steps, computations, and comparisons in the `Actions` section.
2. **Anti-Counterfactual Rule:** Added explicit instructions forbidding agents from computing or asserting the outcome of the opposite scenario just to prove a mismatch.
3. **System-State Preconditions:** Mandated that Preconditions must define objective system states, explicitly banning descriptions of tester knowledge or capabilities.

## 15. Execution State (Iteration 9 - Haiku)

### 15.1 Generation using Haiku
We spawned two isolated subagents (using the fast model) to generate AC-10 (Buy) and AC-11 (Sell) into `intermediate-states/iteration-9-haiku/`. The goal was to check if they can generate highly symmetrical tests following all guided intelligence rules (including explicit formulas, anti-conditionals, and atomicity) directly on the first pass.

### 15.2 Results
Both sub-agents successfully generated the test cases. Key observations:
1. **Symmetry:** Both test cases shared identical structure and phrasing, adjusted only for the Buy vs. Sell side.
2. **Explicit Formula:** The `default_distance` calculation was explicitly defined in Peculiarities rather than using lazy references.
3. **No Conditionals:** Results used unconditional present-tense statements.
4. **Data Partitions:** Valid data partitions were properly marked with `[Valid: ...]` syntax.
5. **Consistent Traceability:** Both inherited the same requirements (DX-WEB-095 as base, with DX-WEB-086 properly added to Sell to match Buy).
Overall, the generation was highly predictable and followed the strict formatting rules perfectly without requiring an auditor to fix major deviations.

## 16. Execution State (Iteration 9 - Gemini)

### 16.1 Generation using Gemini (Default Model)
We repeated the same task from Iteration 9 - Haiku, but this time we spawned two isolated subagents using the default Gemini model to generate AC-10 (Buy) and AC-11 (Sell) into `intermediate-states/iteration-9-gemini/`. 

### 16.2 Results
Both sub-agents successfully generated the test cases, but they demonstrated significant structural drift compared to the Haiku run:
1. **Asymmetry in Phrasing:** Despite having the exact same instructions and rules, the two sub-agents drifted. AC-10 used the label `Stop` price field, while AC-11 used `Order Price` field.
2. **Formula Representation Drift:** AC-10 included the mathematical substitution directly inline in the Results section, whereas AC-11 referenced the computation in Peculiarities.
3. **Traceability Inference:** AC-11 successfully included `DX-WEB-086` based on a prompt constraint to maintain trace symmetry with AC-10.
4. **General Formatting:** The sub-agents correctly followed the physical action constraints (anti-cognitive rules), used imperative verbs, and avoided conditionals in Results.

Overall, this reinforced the finding that parallel isolated agents, even with highly capable models like Gemini, will drift in phrasing and variable naming unless strictly bound by a sequential "Golden Example" or explicitly synchronized by a single auditor sub-agent.

## 17. Execution State (Iteration 9-Audit - Comprehensive Auditor Review & Comparison)

### 17.1 Audit Scope & Methodology
A comprehensive auditor sub-agent was tasked to:
1. Audit iteration-9-haiku test cases against all 7 audit rules
2. Audit iteration-9-gemini test cases against all 7 audit rules
3. Perform structured cross-iteration comparison
4. Generate synthesized improved versions based on audit findings
5. Produce formal JSON audit report and markdown comparative analysis

### 17.2 Audit Findings Summary

**Iteration 9-Haiku Status: FAILED**
- **Critical Rule 3 violations (both files):** Actions sections contained cognitive steps ("Compute default_distance", "Compute expected_default_stop", "Compare the value from step 6 to expected_default_stop"). Per auditor rules, all computations and comparisons must live in Results/Peculiarities, not Actions.
- **Major Rule 4 violations (both files):** Preconditions framed parameter availability as "known from product configuration or the UI" (tester knowledge) rather than purely "platform exposes these values".
- **Strengths:** Strong structural symmetry between Buy and Sell variants; consistent `Stop` price field terminology across both; explicit display-tolerance wording ("within one display tick"); snapshot timing clearly stated.

**Iteration 9-Gemini Status: NEEDS_IMPROVEMENT**
- **AC-10 (Buy):** Mostly compliant. Rule 3 clean (Actions are interactions only). Rule 4 preconditions properly system-oriented. Minor: Summary style uses em dash instead of Object - Action pattern.
- **AC-11 (Sell) Major Issues:**
  - **Rule 5 Major:** Field label divergence: Buy uses `Stop` price field, Sell uses `Order Price` field — inconsistent domain terminology.
  - **Rule 7 Major:** Asymmetry with own Buy variant: field names differ, step ordering narrative differs (Buy records after type selection; Sell records before switch), Options section present only on Sell (rounding deferred to AC-14c).
  - **Rule 4 Major:** Precondition 3 uses "known to the tester" wording (same violation as Haiku, just on the Sell file).
- **Strengths:** Buy case demonstrates proper Action/Result separation; Sell interactions are clean (no cognitive steps).

### 17.3 Cross-Iteration Comparative Analysis

**Dimension Scorecard:**
| Dimension | Winner | Details |
|-----------|--------|---------|
| **Atomicity** | Tie | Both bundle default + freeze checks as one logical flow |
| **Result Phrasing** | Tie | Both use present tense correctly |
| **Action/Result Separation** | Gemini (Buy) | Haiku fails with compute/compare in Actions; Gemini Buy clean |
| **Preconditions Quality** | Gemini Buy | Haiku + Gemini Sell use tester-knowledge language; Gemini Buy is system-state oriented |
| **Terminology Consistency** | Haiku | Haiku uses `Stop` consistently; Gemini diverges (Buy `Stop`, Sell `Order Price`) |
| **Anti-Counterfactual** | Tie | Both clean |
| **Buy/Sell Symmetry** | Haiku | Haiku mirrors perfectly except direction/Ask-Bid; Gemini Sell regresses significantly |

**Better Attempt: TIE (Rationale)**
- Haiku wins on **paired-case symmetry**, **domain terminology**, and **tick tolerance clarity** but fails on **rule 3 fundamentals**.
- Gemini Buy wins on **action-result separation** and **precondition purity** but the iteration as a whole is weakened by the Sell variant's asymmetry, field-naming drift, and reintroduction of tester-knowledge language.
- No single iteration dominates all dimensions; an optimal solution synthesizes Gemini's clean Actions-Result separation applied to both sides, with Haiku's symmetry structure, field labels, tolerance wording, and snapshot ordering.

### 17.4 Synthesized Improved Output
The auditor generated corrected versions combining best practices:

**Improvements Applied:**
1. **Rule 3 Fix:** All computation and comparison logic removed from Actions. Actions contain only: record snapshot → select side → select Stop → read field → wait → read field again. Expected value assertions and formulas live in Results (with formula reference in Peculiarities).
2. **Rule 4 Fix:** Preconditions rewritten to describe platform-exposed state ("The instrument has an active streaming quote feed"; "Platform or contract data exposes Stop/Limit distance, pip_size, and price_increment") with no tester-knowledge phrasing.
3. **Rule 5 & 7 Fix:** Both Buy and Sell use consistent `Stop` price field label; actions mirror except for direction (Buy vs Sell) and side adjustment (Ask+ vs Bid−).
4. **Rule 2 Maintained:** Results remain unconditional present-tense statements.
5. **Options Alignment:** Both files have `Options: None` for consistency (Gemini Sell's AC-14c rounding pointer was dropped to maintain pairing symmetry; can be re-added to both if cross-AC pointers are desired).

**Output Location:** `intermediate-states/iteration-10-haiku-thinking-audit/`
- `10-oe-default-stop-price-buy.md` (improved)
- `11-oe-default-stop-price-sell.md` (improved)
- `audit-report.json` (formal audit findings)
- `comparison-analysis.md` (comparative analysis narrative)

### 17.5 Key Insights for Methodology Evolution

1. **Parallel Agent Drift is Unavoidable Without Synchronization:** Even with explicit rules, two isolated agents (Haiku and Gemini) drifted on field naming, step ordering, and precondition phrasing. A single auditor reading both files simultaneously can catch and correct these asymmetries, but only after generation.

2. **Rule 3 (Action/Result Separation) is a High-Risk Area:** Both iterations showed agent tendency to embed computation and comparison in Actions. This suggests the "anti-cognitive rule" guidance needs to be even more explicit or enforced via lint-style verification before submission.

3. **Precondition Purity (Rule 4) Requires Explicit Vocabulary Constraints:** Agents naturally slide into "tester knowledge" framing (e.g., "values known to the tester") unless the instructions explicitly forbid it and provide positive examples of "system exposure" language.

4. **Paired Test Symmetry Cannot Be Guaranteed by Per-File Prompts:** Generating Buy and Sell in parallel, even with matching rule sets, leads to divergence. Future iterations should either (a) generate from a shared step template with substitution, or (b) generate Buy first, then explicitly pass Buy as a "golden example" to the Sell generator.

### 17.6 Formal Audit Report
Full JSON report saved to: `intermediate-states/iteration-10-haiku-thinking-audit/audit-report.json`

Key sections:
- `audit_iteration`: "9-haiku and 9-gemini comparison"
- `iterations_compared`: Detailed findings for each file in each iteration
- `cross_iteration_comparison`: Strengths, weaknesses, symmetry analysis, verdict
- `final_recommendations`: Priority fixes and future-generation guidance

## 18. Methodology Evolution: Planning Session & Decisions

Following the audit analysis in Iteration 9, a planning session was held to integrate findings into the global methodology. The following decisions and clarifications were made:

### 18.1 Consolidation to Global Methodology
- **Feedback:** The `mock-skill.md` was a case-study specific file and should not be maintained long-term.
- **Decision:** All insights regarding Rule 3 (Action/Result separation) and Rule 4 (Precondition purity) were merged directly into the global `cursor-methodology/.cursor/skills/qa-descriptive-test-generation/SKILL.md`. The mock files will be deleted.

### 18.2 Generalization of Paired-Test Approach (Option B)
- **Feedback:** The "Sequential Generation with Golden Example" approach (Option B) should not be limited to Buy/Sell tests.
- **Decision:** The methodology was updated to generalize this approach for *any* variant testing (e.g., Account/Demo, Standard/Margin, Synchronized/Asynchronous). The rule now dictates a primary/secondary generation pattern with symmetry constraints for any single-dimension variant.

### 18.3 Wave Execution Exception
- **Feedback:** The strict commit-after-phase rule interrupted planned sequences of execution (like simultaneous generation or code-review chains).
- **Decision:** A new "Phase Checkpoint Protocol" (Section 12) was added to `agentic-behavior.mdc` that introduces a **Wave Execution Exception**. Planned waves of related tasks will now run to logical completion before requiring a commit.

### 18.4 Predictability Guidelines
- **Decision:** A new centralized document `predictability-guidelines.md` was created to house the lessons learned from this case study, including parallel vs. sequential trade-offs, vocabulary mapping, and common asymmetry patterns.