---
id: agent-3
phase: 2
scope: dead-code-detection
---

# Dead Code Analysis

<!-- @include: ../_shared/philosophy.md -->

## Scope

Identify and report all dead code including unused imports, exports, unreachable code paths, orphaned files, and code that could be consolidated.

## Analysis Criteria

### 1. Dead Imports

- Imported symbols never used in file
- Namespace imports with only partial usage
- Type imports in JavaScript files

### 2. Dead Exports

- Exported functions/classes never imported elsewhere
- Re-exports that have no consumers
- Public API surface that isn't used

### 3. Dead Code Paths

- Unreachable code after returns/throws
- Conditions that are always true/false
- Switch cases that can never match
- Catch blocks that can never trigger

### 4. Orphaned Files

- Files not imported by any other file
- Test files for non-existent modules
- Config files for removed features

### 5. Commented Code

- Large blocks of commented-out code (>5 lines)
- TODO comments with dead code
- Disabled test cases

### 6. Consolidation Opportunities

- Duplicate code across files
- Similar functions that could be unified
- Copy-pasted logic with minor variations

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `DEAD_IMPORT` | Unused import |
| `DEAD_EXPORT` | Never-imported export |
| `DEAD_CODE_PATH` | Unreachable code |
| `DEAD_ORPHAN_FILE` | Unimported file |
| `DEAD_COMMENTED` | Commented-out code block |
| `DEAD_DUPLICATE` | Consolidation opportunity |

## Examples

### Finding Example

```json
{
  "id": "agent-3-001",
  "file": "src/utils/helpers.ts",
  "line": 1,
  "violation": "DEAD_EXPORT",
  "severity": "LOW",
  "description": "Function 'formatCurrency' is exported but never imported anywhere",
  "currentPattern": "export function formatCurrency(amount: number)",
  "optimalPattern": "Remove if unused, or document why it's kept",
  "migrationPath": "1. Search for all usages, 2. Verify no dynamic imports, 3. Remove export or add usage",
  "recommendation": "Remove unused export or add @internal JSDoc if intentionally kept for future use"
}
```
