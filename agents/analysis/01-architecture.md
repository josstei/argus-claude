---
id: agent-1
phase: 1
scope: architectural-compliance
---

# Architectural Compliance

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze the codebase for architectural violations including process boundary crossings, layer violations, orchestrator thickness, and service responsibility issues.

## Analysis Criteria

### 1. Process Boundary Violations

- Main process code leaking into renderer
- Renderer code importing from main
- Preload exposing unsafe APIs
- Shared code with process-specific dependencies

### 2. Layer Violations

- Infrastructure depending on features
- Features depending on other features (horizontal coupling)
- Circular dependencies between modules
- Lower layers importing higher layers

### 3. Orchestrator Thickness

- Orchestrators containing business logic (should ONLY coordinate)
- Methods with >20 lines of logic in orchestrators
- Orchestrators directly manipulating data instead of delegating

### 4. Service Responsibilities

- Services that are just pass-through wrappers
- Business logic living outside services
- Services not owning their domain fully

### 5. Pattern Improvement Opportunities

- Patterns that work but could be more elegant
- Architectural improvements for long-term maintainability
- Missing abstractions that would improve testability

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `ARCH_BOUNDARY_CROSS` | Process boundary violation |
| `ARCH_LAYER_VIOLATION` | Layer dependency violation |
| `ARCH_ORCHESTRATOR_THICK` | Business logic in orchestrator |
| `ARCH_SERVICE_THIN` | Pass-through service wrapper |
| `ARCH_LOGIC_MISPLACED` | Logic in wrong location |
| `ARCH_PATTERN_SUBOPTIMAL` | Working but improvable pattern |

## Examples

### Finding Example

```json
{
  "id": "agent-1-001",
  "file": "src/renderer/services/file-service.ts",
  "line": 42,
  "violation": "ARCH_BOUNDARY_CROSS",
  "severity": "HIGH",
  "description": "Renderer service directly imports from main process module",
  "currentPattern": "Direct import of main/utils/fs-helper",
  "optimalPattern": "Use IPC bridge to request file operations from main",
  "migrationPath": "1. Create IPC handler in main, 2. Add preload bridge, 3. Update renderer to use bridge",
  "recommendation": "Replace direct import with IPC call through preload bridge"
}
```
