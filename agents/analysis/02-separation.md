---
id: agent-2
phase: 1
scope: separation-of-concerns
---

# Separation of Concerns

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze the codebase for mixed responsibilities, identifying where UI logic, business logic, and cross-cutting concerns are incorrectly placed or intermingled.

## Analysis Criteria

### 1. UI Logic in Services

- DOM manipulation outside UI layer
- CSS class toggling in business logic
- Direct element references in non-UI code
- Window/document access in services

### 2. Business Logic in UI

- Complex calculations in event handlers
- Data transformation in view components
- Validation logic scattered in UI
- State derivation in render methods

### 3. Cross-Cutting Concerns

- Logging inconsistencies (console vs logger)
- Error handling patterns varying across modules
- Configuration access patterns inconsistent
- Authentication checks scattered

### 4. State Management

- State mutations outside designated state handlers
- Duplicate state across services
- Stale state in closures
- Missing single source of truth

### 5. Separation Improvement Opportunities

- Tightly coupled code that could be decoupled
- Interface abstractions for better testability
- Missing dependency injection opportunities

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `SEP_UI_IN_SERVICE` | UI logic in service layer |
| `SEP_LOGIC_IN_UI` | Business logic in UI component |
| `SEP_CROSS_CUT_INCONSISTENT` | Inconsistent cross-cutting concern |
| `SEP_STATE_SCATTERED` | State management violation |
| `SEP_TIGHT_COUPLING` | Unnecessary coupling |
| `SEP_MISSING_ABSTRACTION` | Could benefit from interface |

## Examples

### Finding Example

```json
{
  "id": "agent-2-001",
  "file": "src/services/notification-service.ts",
  "line": 87,
  "violation": "SEP_UI_IN_SERVICE",
  "severity": "MEDIUM",
  "description": "Service directly manipulates DOM to show notification toast",
  "currentPattern": "document.querySelector('.toast').classList.add('visible')",
  "optimalPattern": "Emit event that UI layer handles for display",
  "migrationPath": "1. Create notification event, 2. Move DOM logic to UI handler, 3. Service emits event only",
  "recommendation": "Extract DOM manipulation to dedicated UI component, service should only emit events"
}
```
