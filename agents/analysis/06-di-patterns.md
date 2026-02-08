---
id: agent-6
phase: 4
scope: dependency-injection
---

# DI & Wiring Patterns

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze dependency injection patterns, container registrations, constructor dependencies, and base class compliance for consistency and correctness.

## Analysis Criteria

### 1. Container Registration

- Missing service registrations
- Duplicate registrations for same interface
- Lifecycle mismatches (singleton vs transient)
- Registration order dependencies

### 2. Dependency Declaration

- Constructor dependencies not declared
- Optional dependencies without proper defaults
- Too many constructor parameters (>5)
- Hidden dependencies (global access)

### 3. Base Class Compliance

- Missing super() calls
- Incorrect method overrides
- Abstract method implementations missing
- Lifecycle method sequences violated

### 4. Interface Compliance

- Unimplemented interface methods
- Type signature mismatches
- Missing required properties
- Incorrect return types

### 5. DI Pattern Improvements

- Manual instantiation where DI should be used
- Service locator anti-pattern
- Composition opportunities
- Testability improvements

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `DI_MISSING_REGISTRATION` | Service not registered |
| `DI_DUPLICATE_REGISTRATION` | Double registration |
| `DI_LIFECYCLE_MISMATCH` | Wrong lifecycle scope |
| `DI_CONSTRUCTOR_OVERLOAD` | Too many dependencies |
| `DI_HIDDEN_DEPENDENCY` | Undeclared global access |
| `DI_BASE_CLASS_VIOLATION` | Base class not properly extended |
| `DI_INTERFACE_INCOMPLETE` | Interface not fully implemented |
| `DI_PATTERN_IMPROVE` | DI pattern could be better |

## Examples

### Finding Example

```json
{
  "id": "agent-6-001",
  "file": "src/services/email-service.ts",
  "line": 15,
  "violation": "DI_HIDDEN_DEPENDENCY",
  "severity": "MEDIUM",
  "description": "Service accesses global config directly instead of injecting it",
  "currentPattern": "const config = globalConfig.email",
  "optimalPattern": "constructor(private config: EmailConfig)",
  "migrationPath": "1. Add config to constructor, 2. Register config in container, 3. Update instantiation",
  "recommendation": "Inject EmailConfig through constructor for testability"
}
```
