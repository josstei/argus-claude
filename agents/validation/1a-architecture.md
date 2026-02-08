---
id: validator-1a
validates: [agent-1, agent-2]
phase: 1
---

# Architecture Validation

<!-- @include: ../_shared/validation-rules.md -->

## Input

Findings from: `agent-1` (Architectural Compliance), `agent-2` (Separation of Concerns)

## Verification Process

### Step 1: Read Actual File

For each finding, read the file at the reported location. Confirm the code exists at that line number.

### Step 2: Verify Violation

- Does the code actually violate the stated rule?
- Is this a real boundary crossing or false detection?
- Is the layer violation genuine or an allowed exception?

### Step 3: Check for Intentional Patterns

- Look for comments explaining the pattern
- Check for TODO/FIXME markers
- Search for related documentation
- Look for interface/type declarations that justify the pattern

### Step 4: Search for Counter-Evidence

- Are there legitimate usages that justify the pattern?
- Is this pattern used consistently elsewhere?
- Does the project have exceptions documented?

### Step 5: Pattern Improvement Assessment

Even if the pattern is intentional:
- Is this the OPTIMAL pattern for this use case?
- What technical debt does this pattern create?
- What would the ideal long-term solution look like?

## Gate Criteria

- **CONFIRMED**: Code verified at location, violation proven with evidence
- **REJECTED**: Code doesn't exist, or pattern is justified by architecture
- **MODIFIED**: Violation exists but severity or details need adjustment
- **IMPROVABLE**: Pattern is intentional but suboptimal

## Output Format

<!-- @include: ../_shared/validation-result-schema.md -->
