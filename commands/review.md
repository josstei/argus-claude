---
description: Comprehensive multi-agent code quality and architecture review with cross-validation
---

# Argus Code Review

Multi-agent code review orchestrator with cross-validation from multiple LLM providers.

---

## Configuration

### Step 1: Parse Arguments and Load Config

1. Parse `$ARGUMENTS` for:
   - `--level <fast|balanced|comprehensive>` - Skip confirmation prompt
   - `--scope <path>` - Override scope directory
   - `--output <format>` - Override output format
   - `--yes` or `-y` - Accept defaults without confirmation
   - `--external` - Detect external validators and add any detected tools to `validators`
2. Load `defaults.json` for review levels and options
3. Default to `balanced` level

### Step 2: Confirmation or Execute

**If `--level` OR `--yes` provided:** Skip to Step 3 (no prompt)

**Otherwise, show confirmation prompt:**

```
Argus Code Review
=================
Ready to run with:
  Level:  Balanced (Sonnet, ~5 min)
  Scope:  {detected_scope}
  Output: inline

[1] Change level  [2] Change scope  [3] Change output  [Enter] Start  [Q] Cancel
```

Handle input:
- Enter/empty → proceed with shown settings
- 1 → prompt for level (fast/balanced/comprehensive)
- 2 → prompt for scope path
- 3 → prompt for output format (inline/markdown/json)
- Q/q → abort review

### Step 3: Apply Selected Level

Load the settings from the selected review level:
- `model` - Claude model for agents
- `depth` - Which phases to run
- `strictness` - Issue sensitivity threshold
- `validators` - Validation providers
- `output` - Output format (can be overridden by --output)

If `--external` is set, detected tools are appended to `validators` in Step 6.

### Step 4: Smart Scope Detection

If `scope` is `"auto"` (default), detect in order:
1. Check if `src/` exists → use `src/`
2. Check if `lib/` exists → use `lib/`
3. Check if `app/` exists → use `app/`
4. Check if `packages/` exists → use `packages/` (monorepo)
5. Fall back to `.` (current directory)

### Step 5: Display Summary and Start

```
Argus Code Review
=================
Level:  {selected level}
Model:  {config.model}
Scope:  {detected or config.scope}
Phases: {phases based on depth}

Starting review...
```

### Step 6: Detect External Tools (Conditional)

Only if `--external` was provided:

```bash
codex --version 2>/dev/null && echo "CODEX=true" || echo "CODEX=false"
gemini --version 2>/dev/null && echo "GEMINI=true" || echo "GEMINI=false"
```

If a tool is detected, append it to `validators`. Only detected tools are used. If none are found, display a warning and continue with Claude-only validation.

---

## Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                         ORCHESTRATOR                             │
└─────────────────────────────────────────────────────────────────┘
                                │
    ┌───────────┬───────────┬───┴───┬───────────┬───────────┐
    ▼           ▼           ▼       ▼           ▼           │
┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐      │
│PHASE 1│  │PHASE 2│  │PHASE 3│  │PHASE 4│  │PHASE 5│      │
│ Arch  │  │Hygiene│  │Naming │  │Quality│  │ Perf  │      │
└───┬───┘  └───┬───┘  └───┬───┘  └───┬───┘  └───┬───┘      │
    ▼          ▼          ▼          ▼          ▼           │
┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐      │
│Valid-A│  │Valid-A│  │Valid-A│  │Valid-A│  │Valid-A│      │
└───┬───┘  └───┬───┘  └───┬───┘  └───┬───┘  └───┬───┘      │
    └──────────┴──────────┴────┬────┴──────────┘           │
                               ▼                            │
                    ┌─────────────────┐                    │
                    │  CONSOLIDATION  │◄───────────────────┘
                    └─────────────────┘
```

**CRITICAL**: Spawn ALL analysis agents in a SINGLE message for parallel execution.

---

## Agent Spawning

### Phase 1: Architecture

Spawn in parallel:
- `agents/analysis/01-architecture.md` → Agent 1
- `agents/analysis/02-separation.md` → Agent 2

After completion, spawn:
- `agents/validation/1a-architecture.md` → Validator 1A

### Phase 2: Code Hygiene

Spawn in parallel:
- `agents/analysis/03-dead-code.md` → Agent 3
- `agents/analysis/04-dependencies.md` → Agent 4

After completion, spawn:
- `agents/validation/2a-hygiene.md` → Validator 2A

### Phase 3: Naming

Spawn:
- `agents/analysis/05-naming.md` → Agent 5

After completion, spawn:
- `agents/validation/3a-naming.md` → Validator 3A

### Phase 4: Quality

Spawn in parallel:
- `agents/analysis/06-di-patterns.md` → Agent 6
- `agents/analysis/07-error-handling.md` → Agent 7
- `agents/analysis/08-lifecycle.md` → Agent 8

After completion, spawn:
- `agents/validation/4a-quality.md` → Validator 4A

### Phase 5: Optimization

Spawn:
- `agents/analysis/09-optimization.md` → Agent 9

After completion, spawn:
- `agents/validation/5a-optimization.md` → Validator 5A

### External Validators (Conditional)

If `validators` contains `"codex"`:
- `agents/external/codex.md` → Codex Validator

If `validators` contains `"gemini"`:
- `agents/external/gemini.md` → Gemini Validator

### Consolidation

After ALL validation agents complete:
- `agents/consolidation.md` → Consolidation Agent

---

## Include System

When spawning agents, expand `<!-- @include: path -->` markers:

1. Read agent file content
2. Find all `<!-- @include: path -->` markers
3. Read each referenced file (relative to agent location)
4. Replace markers with file contents
5. Pass expanded content as agent prompt

---

## Model Selection

Use `config.model` for ALL Task spawns:

| Config | Task Parameter |
|--------|----------------|
| `opus` | `model: "opus"` |
| `sonnet` | `model: "sonnet"` |
| `haiku` | `model: "haiku"` |

## Depth-Based Phase Selection

Use `config.depth` to determine which phases to run:

| Depth | Phases |
|-------|--------|
| `quick` | Phase 1 (Architecture), Phase 2 (Hygiene) only |
| `standard` | Phases 1-4 (skip Optimization) |
| `thorough` | All 5 phases |

---

## Command Arguments

Parse `$ARGUMENTS` for options:

| Flag | Description | Example |
|------|-------------|---------|
| `--level <level>` | Select review level (skip prompt) | `--level fast` |
| `--scope <path>` | Override scope directory | `--scope lib/` |
| `--output <format>` | Override output format | `--output json` |
| `--yes`, `-y` | Accept defaults without prompt | `--yes` |
| `--external` | Detect and add external validators if available (only detected tools are used) | `--external` |

Built-in levels: `fast`, `balanced`, `comprehensive`

---

## Quality Gates

```
Startup:
  [ ] Configuration loaded (defaults + user config + args)
  [ ] Scope detected or configured
  [ ] Depth-appropriate phases selected
  [ ] Model resolved

Execution:
  [ ] Analysis agents spawned based on depth setting
  [ ] Validation agents spawn after their phase completes
  [ ] External validators included if in validators array

Consolidation:
  [ ] All validators complete
  [ ] Report generated per schemas/report.json
  [ ] Output displayed inline or saved based on config.output
```

---

## Output

Generate report following `schemas/report.json` structure.

Based on `config.output`:
- `inline` → Display full report in terminal (default)
- `markdown` → Save to `.claude/argus/reports/review-{timestamp}.md`
- `json` → Save to `.claude/argus/reports/review-{timestamp}.json`

Display summary:
```
Review Complete
===============
Files analyzed: {count}
Health score:   {overall}/100

Findings:
  CRITICAL: {count}
  HIGH:     {count}
  MEDIUM:   {count}
  LOW:      {count}
```

If saved to file, append: `Report saved to: {path}`
