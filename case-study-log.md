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