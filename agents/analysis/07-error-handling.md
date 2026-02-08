---
id: agent-7
phase: 4
scope: error-handling-logging
---

# Error Handling & Logging

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze error handling patterns and logging practices for consistency, proper error propagation, and appropriate log levels.

## Analysis Criteria

### 1. Error Handling

- Swallowed errors (empty catch blocks)
- Errors caught but not logged
- Missing error context when re-throwing
- Generic error messages losing specificity

### 2. Logging Practices

- Console.log/error bypassing logger service
- Sensitive data in log messages
- Wrong log levels (debug in production, error for info)
- Missing correlation IDs in async flows

### 3. Async Error Handling

- Unhandled promise rejections
- Missing try/catch in async functions
- Floating promises (not awaited or caught)
- Race conditions in error recovery

### 4. Error Boundaries

- Missing error boundaries in UI
- Cascading failures not contained
- No graceful degradation
- Missing fallback behaviors

### 5. Error Strategy Improvements

- Custom error classes for domains
- Error categorization (recoverable vs fatal)
- Retry strategies for transient errors
- Circuit breaker patterns

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `ERR_SWALLOWED` | Error caught but not handled |
| `ERR_NOT_LOGGED` | Error should be logged |
| `ERR_CONTEXT_LOST` | Re-throw loses context |
| `LOG_BYPASS_LOGGER` | Console used instead of logger |
| `LOG_SENSITIVE_DATA` | PII or secrets in logs |
| `LOG_WRONG_LEVEL` | Inappropriate log level |
| `ERR_ASYNC_UNHANDLED` | Unhandled async error |
| `ERR_FLOATING_PROMISE` | Promise not awaited |
| `ERR_STRATEGY_IMPROVE` | Error strategy could be better |

## Examples

### Finding Example

```json
{
  "id": "agent-7-001",
  "file": "src/services/api-client.ts",
  "line": 45,
  "violation": "ERR_SWALLOWED",
  "severity": "HIGH",
  "description": "API error caught with empty catch block, failures go unnoticed",
  "currentPattern": "catch (e) { }",
  "optimalPattern": "catch (e) { logger.error('API call failed', { error: e, endpoint }); throw new ApiError(e); }",
  "migrationPath": "1. Log the error, 2. Wrap in domain error, 3. Re-throw or handle gracefully",
  "recommendation": "Log error with context and either re-throw or implement proper fallback"
}
```
