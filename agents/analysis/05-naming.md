---
id: agent-5
phase: 3
scope: naming-conventions
---

# Naming Convention Audit

<!-- @include: ../_shared/philosophy.md -->

## Scope

Comprehensive review of naming conventions across files, directories, classes, methods, variables, and events to ensure consistency and semantic clarity.

## Analysis Criteria

### 1. File Naming

- Pattern: `kebab-case.type.ts` (e.g., `user-service.ts`, `auth.controller.ts`)
- Type suffixes: `.service.ts`, `.controller.ts`, `.model.ts`, `.utils.ts`
- Test files: `*.test.ts` or `*.spec.ts`

### 2. Directory Naming

- Pattern: `kebab-case/`
- Feature folders: `features/user-management/`
- No abbreviations: `utilities/` not `utils/`

### 3. Class/Constructor Naming

- Pattern: `PascalCase`
- Suffix matches purpose: `UserService`, `AuthController`, `UserModel`
- No abbreviations: `DatabaseConnection` not `DbConn`

### 4. Method Naming

- Pattern: `camelCase` with verb prefixes
- Actions: `create`, `update`, `delete`, `get`, `set`, `handle`, `process`
- Booleans: `is`, `has`, `can`, `should` return types

### 5. Variable Naming

- Pattern: `camelCase`
- Booleans: `isActive`, `hasPermission`, `canEdit`, `shouldRefresh`
- Collections: plural (`users`, `items`)
- Constants: `SCREAMING_SNAKE_CASE`

### 6. Event Naming

- Pattern: `domain:action` (e.g., `user:created`, `auth:logout`)
- Past tense for completed: `session:expired`
- Present for ongoing: `file:uploading`

### 7. Semantic Clarity

- Names convey intent without needing comments
- Avoid generic names: `data`, `info`, `item`, `thing`
- Domain-specific vocabulary consistently used

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `NAME_FILE_PATTERN` | File naming violation |
| `NAME_DIR_PATTERN` | Directory naming violation |
| `NAME_CLASS_PATTERN` | Class naming violation |
| `NAME_METHOD_PATTERN` | Method naming violation |
| `NAME_VAR_PATTERN` | Variable naming violation |
| `NAME_EVENT_PATTERN` | Event naming violation |
| `NAME_SEMANTIC_UNCLEAR` | Name lacks clarity |

## Examples

### Finding Example

```json
{
  "id": "agent-5-001",
  "file": "src/services/usr-svc.ts",
  "line": 1,
  "violation": "NAME_FILE_PATTERN",
  "severity": "LOW",
  "description": "File name uses abbreviations instead of full words",
  "currentPattern": "usr-svc.ts",
  "optimalPattern": "user-service.ts",
  "migrationPath": "1. Rename file, 2. Update all imports, 3. Update any path references",
  "recommendation": "Rename to user-service.ts for clarity and consistency"
}
```
