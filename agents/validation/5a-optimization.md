---
id: validator-5a
validates: [agent-9]
phase: 5
---

# Optimization Validation

<!-- @include: ../_shared/validation-rules.md -->

## Input

Findings from: `agent-9` (Performance & Optimization)

## Verification Process

### Step 1: Verify Hot Path

- Is this code actually in a performance-critical path?
- How frequently is it executed?
- Is it in a loop, event handler, or render cycle?

### Step 2: Measure Actual Impact

Consider:
- Data size typically processed
- Frequency of operation
- User-facing latency impact
- Memory footprint change

### Step 3: Check for Side Effects

For parallelization suggestions:
- Are operations truly independent?
- Any shared state mutations?
- Order dependencies?

For caching suggestions:
- What's the cache invalidation strategy?
- Memory vs speed tradeoff
- Stale data risks?

### Step 4: Verify Optimization Correctness

- Will suggested change preserve behavior?
- Any edge cases not considered?
- Platform compatibility concerns?

### Step 5: Cost-Benefit Analysis

- Implementation effort vs performance gain
- Code complexity increase
- Maintainability impact
- Testing requirements

## Gate Criteria

- **CONFIRMED**: Performance issue verified, optimization is safe
- **REJECTED**: Not a hot path, or optimization unsafe
- **MODIFIED**: Issue exists but different optimization recommended
- **IMPROVABLE**: Works but measurable improvement possible

## Output Format

<!-- @include: ../_shared/validation-result-schema.md -->
