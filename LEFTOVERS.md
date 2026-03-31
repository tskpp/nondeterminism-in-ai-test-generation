# Leftover Issues & Deferred Decisions

## Issue: Sources Discovery & Collection Strategy

**Status:** DEFERRED (Iteration-11 Planning)

**Context:**
During iteration-11 planning (AC-22, AC-23 test case generation), the team identified a critical methodological gap: how should test cases obtain and validate data sources (FX conversion rates, instrument metadata, live quotes, etc.) without blocking test generation waves?

**Approaches Evaluated:**

### Approach A: Approval Registry (Rejected)
- Pre-populated registry of all approved data sources
- Sources owned/maintained by specific people (Risk team, Platform team, etc.)
- **Issue:** Heavyweight process contradicts goal of reducing human effort; sources were assumed to already exist

### Approach B: Automated Discovery (Rejected)
- Sources publish self-test descriptors
- Daily CI pipeline discovers, validates, and updates registry
- **Issue:** Over-complicated; requires infrastructure changes; doesn't address generation-time source uncertainty

### Approach C: Generative + Dynamic (Selected for Further Implementation)
- Sources identified during test generation based on requirement analysis
- Missing sources resolved dynamically at execution time (non-blocking)
- Optional: User provides source hints upfront (optional-source-hints.yaml)
- **Benefit:** Generation wave never blocked; sources resolved per-test during execution
- **Status:** Requires implementation and testing

**Decision Made:**
Approach C will be implemented in Iteration-12+. Test cases will include source references in Peculiarities section with resolution strategy (auto-fetch endpoints + user fallback).

**Implementation Needed:**
1. Update test-case-template to include "Source References" section in Peculiarities
2. Create source-resolution-executor that implements Priority 1-3 logic (cache → auto-fetch → user prompt)
3. Generate optional-source-hints.yaml documentation
4. Test with AC-22/AC-23 re-execution

**Files Affected:**
- `.cursor/skills/qa-descriptive-test-generation/SKILL.md` (Phase 4: Source Resolution)
- `qa-sessions/web-otc-order-entry/optional-source-hints.yaml` (user-provided hints)
- Test case templates (add Source References section)

**Risk:**
- User prompts during execution could slow test runs if many sources are missing
- Mitigation: Cache results aggressively; pre-document common sources

**Follow-up:**
- Schedule Iteration-12 to implement source-resolution-executor
- Test with AC-22/AC-23 pair first
- Expand to other test types after validation
