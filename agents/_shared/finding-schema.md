---
type: shared
used-by: [analysis]
---

# Finding Schema

## Purpose

Defines the standard output format for all analysis agent findings. Consistent structure enables validation agents and consolidation to process findings uniformly.

## Schema

```json
{
  "id": "string (unique identifier: {agent-id}-{sequence})",
  "file": "string (relative path from project root)",
  "line": "number (1-indexed line number)",
  "violation": "string (violation code from agent's type table)",
  "severity": "CRITICAL | HIGH | MEDIUM | LOW | IMPROVEMENT",
  "description": "string (clear explanation of the issue)",
  "currentPattern": "string (description of what exists now)",
  "optimalPattern": "string (what the long-term ideal looks like)",
  "migrationPath": "string (steps to evolve from current to optimal)",
  "recommendation": "string (specific actionable fix)",
  "codeSnippet": "string (relevant code excerpt, max 10 lines)"
}
```

## Severity Definitions

| Level | Meaning | Action |
|-------|---------|--------|
| `CRITICAL` | Security risk, data loss, or system failure | Must fix immediately |
| `HIGH` | Significant bug or architectural violation | Fix in current sprint |
| `MEDIUM` | Code quality issue or pattern violation | Plan to address |
| `LOW` | Minor improvement opportunity | Address when touching file |
| `IMPROVEMENT` | Not broken, but could be better | Consider for refactoring |

## Required Fields

All findings MUST include: `id`, `file`, `line`, `violation`, `severity`, `description`, `recommendation`

Optional fields: `currentPattern`, `optimalPattern`, `migrationPath`, `codeSnippet`
