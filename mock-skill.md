---
name: mock-qa-descriptive-test-generation
description: |
  Generates fully structured, descriptive manual test cases based on an approved test list according to ISTQB and Confluence policies. Serves as Phase 3 of the QA workflow.

  Use when: "generate descriptive tests", "create manual test cases", "expand test list", "write detailed test cases"
---

# QA Descriptive Test Generation (Mocked for Predictability Case Study)

This skill is Phase 3 of the QA workflow. It takes an approved `test-list.md` and generates structured, atomic, descriptive test cases. These tests act as the explicit blueprint for subsequent automated Integration and E2E testing. 

**Input:** Path to an approved `test-list.md`.
**Output:** Individual descriptive test cases stored in a dedicated directory.

## Phase 0: Load Context

1. Identify the working directory.
2. Read the approved `test-list.md` generated from Phase 2. Ensure you read and store the Frontmatter (Global Component Name, Global Test Items, Standard Preconditions).
3. If not already present, create a `descriptive-tests/` subdirectory within the working directory.
4. Read the test-case rules by reviewing `cursor-methodology/.cursor/skills/test-master/references/test-case-auditor.md`.

## Phase 0.1: Generation Planning (Anti-Mass-Generation)

1. Review the `test-list.md`. 
2. You MUST NOT generate all test cases at once. 
3. Propose a small initial batch of 3-4 highly representative test cases (e.g., a paired Buy/Sell scenario).
4. Explicitly output: `[WAITING FOR USER APPROVAL] I propose generating the following initial test cases: [List]. Do you approve this batch for generation?` and HALT.

## Phase 0.5: Guided Intelligence & Golden Example Context

Do NOT rely on copying adjacent files. You must apply the following explicit formatting intelligence rules to ensure systemic predictability.

**Golden Example Check:** If the user has already approved a previous batch of tests, read them to inherit descriptive style and phrasing. However, **Critical Override:** You must NEVER copy a style from a golden example that violates core rules (e.g., you must ignore conditional "when" clauses in Results even if present in the golden example).

1. **UI Element Highlighting:** Only use single backticks for UI elements, buttons, tabs, and sides (e.g., `Buy`). Do NOT use double backticks, do NOT wrap spaces, and do NOT wrap non-UI elements.
2. **Action Phrasing:** Actions must strictly start with an imperative verb (e.g., *Open*, *Select*, *Switch*, *Input*).
3. **Result Structuring:** If there are multiple results, they MUST be formatted as a bulleted list. Never use narrative paragraphs.
4. **Data Partition Syntax:** Inline data partitions must strictly follow the format: `[Valid/Invalid: explicit value]` without backticks around them, placed at the end of the relevant action step.
5. **Traceability Inheritance & Source Validation:** When generating a pair or group of test cases derived from the same base functionality (e.g., Buy vs Sell variants), they MUST inherit the exact same Requirement IDs (`[Req-ID]`) from the test list. If the source `test-list.md` itself is asymmetric (e.g., assigning a requirement to the Buy case but missing it on the Sell case), the auditor and generator MUST flag the `test-list.md` as flawed and halt, rather than silently dropping the requirement.
6. **Peculiarities Preservation:** If a Peculiarity or Options rule applies to the functional logic of a paired test case, it must be identical across both cases.
7. **Peculiarity Clarity (Anti-Lazy References):** Do NOT use vague pointers or "black box" links (e.g., "per Confluence rules", "per external rules"). If the formula or calculation logic is available in the test-list or requirements, you MUST explicitly define the logic inline within the test case.
8. **Peculiarities Purity:** The `Peculiarities` section is strictly for definitions, formulas, and environment variables. You MUST NOT place execution guidance, comparison instructions, or expected functional outcomes here. All execution/comparison steps belong in `Actions`.
9. **Strict Result Phrasing:** The `Results` section MUST NOT contain the words "when" or "if". If an outcome depends on a timing condition (e.g., waiting for a quote update), that timing condition MUST be a dedicated Action step, and the Result must describe the state as an unconditional fact after that step.

## Phase 1: Test Case Expansion

For each test header defined in the approved batch from Phase 0.1, generate an individual markdown file in the `descriptive-tests/` folder. Name the files logically (e.g., `01-login-success.md`).

You MUST adhere to the following strict rules for every test case:
- **Atomicity:** One test file equals one explicit test.
- **Tense:** Use present tense for the `Results` section (e.g., "The system saves"). No "should" or "if/when".
- **Unambiguous Results:** The Results section contains only the final state verification. Do not correlate intermediate observations. Any conditions must be in the Actions section, NOT in the Results section.
- **Action/Result Separation:** Actions contain only inputs and interactions. Results contain only the outcomes.
- **Explicit Data Partitioning & Segregation:** Generic input placeholders MUST be supplemented with brackets detailing explicit valid and/or invalid partitions (e.g., `[Valid: 1.50]`). Positive functional tests must use ONLY valid partitions.
- **Global Variables:** Use the `Global Component Name`, `Global Test Items`, and `Standard Preconditions` from the `test-list.md` frontmatter EXACTLY as written. Do not improvise these values.

### Strict Formatting Template

You MUST use the following exact template for every generated file. Ensure you pull BOTH the Test List ID and the Requirement ID from `test-list.md`.

```markdown
# Summary
[Object] - [Action]

**Traceability:** 
- Test List: `[AC-ID]`
- Requirement: `[Req-ID]`

# Component
[Global Component Name from test-list.md frontmatter]

# Severity
[Must be: Showstopper, Functional, Minor functional, Usability, or Glitch]

# Test Items
[Global Test Items from test-list.md frontmatter]

# Preconditions
[Standard Preconditions from test-list.md frontmatter exactly as written]
[N. Any specific preconditions for this test]

# Actions
1. [Action 1]
2. [Action 2] [Valid/Invalid: explicit partition]

# Results
- [Unconditional present-tense final outcome.]

# Options
None

# Peculiarities
None
```

## Phase 2: Validation & Handoff

1. Run a self-check on the generated files to ensure all mandatory headers are present and the Results sections contain no conditional logic.
2. Launch the `test-case-auditor` subagent via the Task tool to verify the draft test cases. 

## Phase 3: Finding Remediation & Hard Stop

1. Review the `test-case-auditor` JSON findings.
2. Address ALL findings of **critical** and **major** severity.
3. Explicitly output: `[WAITING FOR USER APPROVAL] Please review the generated descriptive test cases. Do you approve them for finalization?`
