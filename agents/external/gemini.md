---
id: external-gemini
tool: gemini
detection: "gemini --version"
---

# Gemini Validator

## Prerequisites

- Tool: `gemini` (Google Gemini CLI)
- Detection: `gemini --version 2>/dev/null`

## Invocation

```bash
gemini analyze {scope.directory} \
  --focus "{analysis_focus}" \
  --exclude "node_modules,dist,build,.git" \
  --output json \
  --format structured
```

Alternative prompt-based invocation:

```bash
gemini prompt "Analyze the code in {scope.directory} for {concern}.

Output as JSON array:
[
  {
    \"path\": \"file path\",
    \"lineNumber\": 42,
    \"type\": \"issue type\",
    \"severity\": \"critical|high|medium|low\",
    \"message\": \"issue description\",
    \"fix\": \"suggested fix\"
  }
]

Focus: {analysis_focus}"
```

## Input Mapping

| Argus Concept | Gemini Equivalent |
|---------------|-------------------|
| Phase 1 (Architecture) | "code architecture, layering, module boundaries" |
| Phase 2 (Hygiene) | "dead code detection, import analysis, code freshness" |
| Phase 3 (Naming) | "naming conventions, identifier clarity, consistency" |
| Phase 4 (Quality) | "error handling, resource management, patterns" |
| Phase 5 (Optimization) | "performance optimization, efficiency, async patterns" |

## Output Parsing

Gemini returns JSON. Map to Argus finding format:

```
Gemini field → Argus field
--------------------------
path         → file
lineNumber   → line
type         → violation (map to Argus codes)
severity     → severity (normalize: critical→CRITICAL, high→HIGH, etc.)
message      → description
fix          → recommendation
```

Add fields:
- `id`: Generate as `gemini-{phase}-{sequence}`
- `source`: `"gemini"`

## Severity Normalization

| Gemini | Argus |
|--------|-------|
| `critical` | `CRITICAL` |
| `high` | `HIGH` |
| `medium` | `MEDIUM` |
| `low` | `LOW` |
| `info` | `IMPROVEMENT` |

## Error Handling

If Gemini returns non-JSON:
- Log the raw output
- Skip this validator for the phase
- Note in consolidation that Gemini validation was skipped
