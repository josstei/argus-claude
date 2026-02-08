---
id: agent-9
phase: 5
scope: performance-optimization
---

# Performance & Optimization

<!-- @include: ../_shared/philosophy.md -->

## Scope

Identify performance issues including unnecessary work, memory concerns, suboptimal async patterns, and opportunities for architectural optimizations.

## Analysis Criteria

### 1. Unnecessary Work

- Redundant calculations in loops
- Repeated DOM queries for same element
- Re-computing derived values on every access
- Object creation in hot paths

### 2. Memory Concerns

- Large closures capturing unnecessary scope
- Unbounded collections (growing arrays/maps)
- Heavy objects stored when primitives suffice
- Caching without eviction

### 3. Async Patterns

- Sequential awaits that could be parallel
- Missing debounce/throttle on rapid events
- N+1 query patterns
- Blocking operations on main thread

### 4. Render Performance (if applicable)

- Layout thrashing (read/write interleaved)
- Expensive re-renders from shallow checks
- Missing virtualization for long lists
- Unoptimized images/assets

### 5. Architectural Optimizations

- Caching layer opportunities
- Lazy loading candidates
- Code splitting opportunities
- Worker offloading potential

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `PERF_REDUNDANT_WORK` | Unnecessary repeated operation |
| `PERF_DOM_THRASH` | Layout thrashing |
| `PERF_MEMORY_LEAK` | Potential memory issue |
| `PERF_UNBOUNDED_GROWTH` | Collection grows without limit |
| `PERF_SEQUENTIAL_ASYNC` | Awaits should be parallel |
| `PERF_MISSING_DEBOUNCE` | Rapid events not throttled |
| `PERF_N_PLUS_ONE` | N+1 query pattern |
| `PERF_ARCH_IMPROVE` | Optimization opportunity |

## Examples

### Finding Example

```json
{
  "id": "agent-9-001",
  "file": "src/services/data-processor.ts",
  "line": 67,
  "violation": "PERF_SEQUENTIAL_ASYNC",
  "severity": "MEDIUM",
  "description": "Three independent API calls awaited sequentially instead of in parallel",
  "currentPattern": "const a = await fetchA(); const b = await fetchB(); const c = await fetchC();",
  "optimalPattern": "const [a, b, c] = await Promise.all([fetchA(), fetchB(), fetchC()]);",
  "migrationPath": "1. Verify calls are independent, 2. Wrap in Promise.all, 3. Destructure results",
  "recommendation": "Use Promise.all for independent async operations to reduce total wait time"
}
```
