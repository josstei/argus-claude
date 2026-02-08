---
id: validator-2a
validates: [agent-3, agent-4]
phase: 2
---

# Code Hygiene Validation

<!-- @include: ../_shared/validation-rules.md -->

## Input

Findings from: `agent-3` (Dead Code Analysis), `agent-4` (Dependency Health)

## Verification Process

### Step 1: Exhaustive Usage Search

For dead code findings:
- Grep for the symbol name across entire codebase
- Search with different cases (camelCase, snake_case variants)
- Check for string-based dynamic references

### Step 2: Check Dynamic Patterns

- Look for dynamic imports: `import()`, `require()`
- Check for string-based method calls: `obj[methodName]()`
- Search for reflection patterns
- Check webpack/bundler configuration for aliases

### Step 3: Verify Test-Only Usage

- Check if symbol is used in test files
- Verify if it's an intentional test-only export
- Look for @internal or @private JSDoc tags

### Step 4: Validate Orphaned Files

For orphaned file findings:
- Check build configuration for entry points
- Search for dynamic file loading
- Verify asset references in HTML/templates
- Check for standalone scripts

### Step 5: Circular Dependency Verification

For circular dependency findings:
- Trace the full import chain
- Confirm the cycle exists
- Check if lazy imports break the cycle at runtime

## Gate Criteria

- **CONFIRMED**: Zero usages found after exhaustive search
- **REJECTED**: Legitimate usage found (document where)
- **MODIFIED**: Finding valid but classification needs update
- **IMPROVABLE**: Code works but structure could be cleaner

## Output Format

<!-- @include: ../_shared/validation-result-schema.md -->
