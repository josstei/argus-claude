---
id: agent-4
phase: 2
scope: dependency-health
---

# Dependency & Import Health

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze import patterns for circular dependencies, incorrect dependency direction, organizational issues, and module boundary violations.

## Analysis Criteria

### 1. Import Organization

- Inconsistent import ordering
- Missing path aliases where configured
- Relative imports that could use aliases
- Mixed default/named imports for same module

### 2. Circular Dependencies

- Direct circular dependencies (A → B → A)
- Indirect circular chains (A → B → C → A)
- Circular type dependencies
- Lazy import workarounds hiding cycles

### 3. Dependency Direction

- Lower layers importing higher layers
- Infrastructure importing from features
- Shared code importing from specific features
- Utilities depending on domain objects

### 4. Module Boundary Violations

- Internal module files imported directly
- Bypassing index.ts barrel exports
- Accessing private module internals
- Cross-feature direct imports

### 5. Dependency Structure Improvements

- Missing barrel exports for modules
- Inconsistent re-export patterns
- Module boundaries that need clarification

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `DEP_IMPORT_ORDER` | Import organization issue |
| `DEP_CIRCULAR_DIRECT` | Direct circular dependency |
| `DEP_CIRCULAR_INDIRECT` | Indirect circular chain |
| `DEP_DIRECTION_WRONG` | Incorrect dependency direction |
| `DEP_BOUNDARY_BYPASS` | Module boundary violation |
| `DEP_STRUCTURE_IMPROVE` | Structure improvement opportunity |

## Examples

### Finding Example

```json
{
  "id": "agent-4-001",
  "file": "src/features/auth/login.ts",
  "line": 3,
  "violation": "DEP_CIRCULAR_DIRECT",
  "severity": "HIGH",
  "description": "Circular dependency: auth/login → auth/session → auth/login",
  "currentPattern": "login imports session, session imports login for validation",
  "optimalPattern": "Extract shared validation to separate module",
  "migrationPath": "1. Identify shared code, 2. Create auth/validation module, 3. Update imports",
  "recommendation": "Extract validateCredentials to auth/validation.ts to break the cycle"
}
```
