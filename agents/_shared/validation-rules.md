---
type: shared
used-by: [validation]
---

# Validation Rules

## Purpose

Defines the contract for all validation agents. Validation agents verify findings from analysis agents, ensuring only proven issues reach the final report.

## Core Rules

### 1. Never Trust, Always Verify

Read the actual file at the reported location. Confirm the code exists at that line. Verify it actually violates the stated rule.

### 2. Search Exhaustively

Use multiple search patterns when checking for usages. Check dynamic imports/requires. Check string-based references. Check test files for intentional test-only exports.

### 3. Consider Context

Check for intentional design decisions (look for comments). Search for usages that justify the pattern. Consider platform or framework requirements.

### 4. Challenge Optimality

Even if a pattern is intentional and "works", evaluate: Is this the OPTIMAL pattern? What technical debt does this create? What would the ideal long-term solution look like?

### 5. Document Evidence

Every validation decision requires proof. Include code snippets. Reference search results. Explain reasoning clearly.

### 6. No Speculation

Only report what you can prove. If uncertain, mark as NEEDS_REVIEW rather than confirming or rejecting.

## Validation Statuses

| Status | Meaning | Criteria |
|--------|---------|----------|
| `CONFIRMED` | Finding is valid | Code verified, violation proven |
| `REJECTED` | Finding is false positive | Evidence disproves the finding |
| `MODIFIED` | Finding valid but needs adjustment | Severity or details corrected |
| `IMPROVABLE` | Pattern works but isn't optimal | Intentional but suboptimal |
| `NEEDS_REVIEW` | Insufficient evidence | Requires human judgment |
