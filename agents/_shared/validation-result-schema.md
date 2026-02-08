---
type: shared
used-by: [validation]
---

# Validation Result Schema

## Purpose

Defines the standard output format for validation agent results. Links back to original findings and provides evidence for decisions.

## Schema

```json
{
  "originalFindingId": "string (ID from analysis finding)",
  "validationStatus": "CONFIRMED | REJECTED | MODIFIED | IMPROVABLE | NEEDS_REVIEW",
  "evidence": {
    "fileRead": "boolean (was the file actually read)",
    "lineVerified": "boolean (does the line contain the reported code)",
    "codeSnippet": "string (actual code found at location)",
    "searchesPerformed": ["string (search patterns used)"],
    "usagesFound": "number (count of legitimate usages)"
  },
  "notes": "string (explanation of validation decision)",
  "modifications": {
    "severity": "string | null (corrected severity if MODIFIED)",
    "description": "string | null (corrected description if MODIFIED)",
    "recommendation": "string | null (corrected recommendation if MODIFIED)"
  },
  "patternAssessment": {
    "isIntentional": "boolean (appears to be by design)",
    "isOptimal": "boolean (is this the best approach)",
    "optimalPatternDescription": "string | null (better approach if not optimal)",
    "migrationSteps": ["string (steps to improve)"],
    "technicalDebtCost": "LOW | MEDIUM | HIGH (impact of keeping current pattern)"
  }
}
```

## Required Fields

All results MUST include: `originalFindingId`, `validationStatus`, `evidence.fileRead`, `notes`

For `CONFIRMED`: `evidence.lineVerified`, `evidence.codeSnippet`

For `REJECTED`: `evidence.searchesPerformed`, reason in `notes`

For `MODIFIED`: `modifications` object with changes

For `IMPROVABLE`: `patternAssessment` object
