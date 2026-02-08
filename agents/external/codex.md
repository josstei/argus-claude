---
id: external-codex
tool: codex
detection: "codex --version"
---

# Codex Validator

## Prerequisites

- Tool: `codex` (OpenAI Codex CLI)
- Detection: `codex --version 2>/dev/null`

## Invocation

```bash
codex exec "Analyze the codebase at {scope.directory} for {concern}.

Report findings in this JSON format:
{
  \"findings\": [
    {
      \"file\": \"path/to/file\",
      \"line\": 42,
      \"issue\": \"description\",
      \"severity\": \"HIGH|MEDIUM|LOW\",
      \"suggestion\": \"recommended fix\"
    }
  ]
}

Focus on: {analysis_focus}
Exclude: node_modules, dist, build, .git"
```

## Input Mapping

| Argus Concept | Codex Equivalent |
|---------------|------------------|
| Phase 1 (Architecture) | "architectural violations, layer boundaries, process separation" |
| Phase 2 (Hygiene) | "dead code, unused exports, circular dependencies" |
| Phase 3 (Naming) | "naming convention violations, unclear identifiers" |
| Phase 4 (Quality) | "error handling issues, resource leaks, DI problems" |
| Phase 5 (Optimization) | "performance issues, memory concerns, async patterns" |

## Output Parsing

Codex returns JSON. Map to Argus finding format:

```
Codex field → Argus field
--------------------------
file        → file
line        → line
issue       → description
severity    → severity (normalize to Argus levels)
suggestion  → recommendation
```

Add fields:
- `id`: Generate as `codex-{phase}-{sequence}`
- `violation`: Infer from issue type
- `source`: `"codex"`

## Error Handling

If Codex returns non-JSON:
- Log the raw output
- Skip this validator for the phase
- Note in consolidation that Codex validation was skipped
