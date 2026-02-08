---
id: validator-3a
validates: [agent-5]
phase: 3
---

# Naming Validation

<!-- @include: ../_shared/validation-rules.md -->

## Input

Findings from: `agent-5` (Naming Convention Audit)

## Verification Process

### Step 1: Verify the Actual Name

- Read the file at the reported location
- Confirm the name exists as reported
- Check the full context (class, function, variable)

### Step 2: Check for Valid Exceptions

External API requirements:
- Third-party library conventions
- Platform-specific naming (DOM APIs, Node APIs)
- Protocol requirements (HTTP headers, JSON keys)

Framework conventions:
- React: `useState`, `useEffect` patterns
- Express: `req`, `res`, `next` parameters
- Test frameworks: `describe`, `it`, `expect`

### Step 3: Assess Impact

- How many files would change if renamed?
- Are there breaking API changes?
- Is this a public vs internal symbol?

### Step 4: Semantic Quality Assessment

Even if technically compliant:
- Does the name clearly convey purpose?
- Would a new developer understand it?
- Is domain vocabulary used correctly?

### Step 5: Consistency Check

- Is this name consistent with similar items?
- Does the project use this pattern elsewhere?
- Is there a more common convention?

## Gate Criteria

- **CONFIRMED**: Name violates convention, no valid exception
- **REJECTED**: Valid external API or framework exception
- **MODIFIED**: Violation but severity needs adjustment
- **IMPROVABLE**: Technically valid but could be clearer

## Output Format

<!-- @include: ../_shared/validation-result-schema.md -->
