# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Argus is a Claude Code plugin for multi-agent code quality and architecture review with cross-validation from multiple LLM providers. Named after Argus Panoptes, the all-seeing giant of Greek mythology.

## Architecture

### Separation of Concerns

- `commands/` - Entry points only (skills that Claude Code exposes as `/argus:*`). Orchestration logic lives in `commands/review.md`.
- `agents/` - Individual agent definitions, each with a single responsibility.
- `agents/_shared/` - Reusable content fragments included via `<!-- @include: path -->` markers.
- `schemas/` - JSON schemas that define the data contracts between agents.

### Multi-Agent Pipeline

```
Orchestrator (commands/review.md)
  → Analysis Phase: up to 9 agents run in parallel per phase grouping
  → Validation Phase: 5 validators verify findings with evidence
  → External Phase (optional): Codex/Gemini CLI cross-validation
  → Consolidation: merges, deduplicates, scores, generates report
```

### Phase-to-Agent Mapping

| Phase | Analysis Agents | Validator | Depth Required |
|-------|----------------|-----------|----------------|
| 1: Architecture | 01-architecture, 02-separation | 1a-architecture | quick+ |
| 2: Hygiene | 03-dead-code, 04-dependencies | 2a-hygiene | quick+ |
| 3: Naming | 05-naming | 3a-naming | standard+ |
| 4: Quality | 06-di-patterns, 07-error-handling, 08-lifecycle | 4a-quality | standard+ |
| 5: Optimization | 09-optimization | 5a-optimization | thorough |

### Data Flow Between Agents

```
Analysis agents → finding.json schema → Validation agents → validation-result.json schema → Consolidation → report.json schema
```

- **finding.json**: Individual issue with `id`, `file`, `line`, `violation`, `severity`, `description`, `recommendation` (required), plus optional `currentPattern`, `optimalPattern`, `migrationPath`, `codeSnippet`
- **validation-result.json**: Links back to `originalFindingId` with `validationStatus` (CONFIRMED/REJECTED/MODIFIED/IMPROVABLE/NEEDS_REVIEW) and required `evidence` object
- **report.json**: Final consolidated output with `healthScore` (0-100 per domain), `verifiedFindings`, `implementationPlan` (phased by severity), and optional `patternEvolutionRoadmap`

### Include System

Agent files use `<!-- @include: relative/path -->` markers. The orchestrator expands these before spawning agents. Paths are relative to the agent file's location (e.g., `<!-- @include: ../_shared/philosophy.md -->`).

### Agent File Templates

**Analysis agents** follow: YAML frontmatter (`id`, `phase`, `scope`) → `<!-- @include: philosophy.md -->` → Scope section → Analysis Criteria → `<!-- @include: finding-schema.md -->` → Violation type table → Examples

**Validation agents** follow: YAML frontmatter (`id`, `validates[]`, `phase`) → `<!-- @include: validation-rules.md -->` → Input section → Verification steps → Gate criteria → `<!-- @include: validation-result-schema.md -->`

**Shared components** follow: YAML frontmatter (`type: shared`, `used-by[]`) → Purpose → Content

## Commands

| Command | Description |
|---------|-------------|
| `/argus:review` | Run code review (shows confirmation prompt) |
| `/argus:config` | View review levels and settings |
| `/argus:config set scope <path>` | Set default scope |
| `/argus:config set output <format>` | Set default output format |
| `/argus:help` | Show help and workflows |
| `/argus:welcome` | Getting started guide |

### Command Arguments

```bash
/argus:review --level fast        # Skip prompt, use Fast level
/argus:review --level balanced    # Skip prompt, use Balanced level
/argus:review --yes               # Skip prompt, use defaults
/argus:review --scope lib/        # Override scope directory
/argus:review --output markdown   # Override output format
/argus:review --external          # Enable detected external validators (Codex, Gemini)
```

## Review Levels

| Level | Model | Depth | Strictness | Agents | Token Usage |
|-------|-------|-------|------------|--------|-------------|
| **Fast** | Haiku | quick | lenient | 4 analysis + 2 validators | Low (~2 min) |
| **Balanced** | Sonnet | standard | balanced | 8 analysis + 4 validators | Moderate (~5 min) |
| **Comprehensive** | Opus | thorough | strict | 9 analysis + 5 validators | Higher (~8 min) |

Strictness controls issue sensitivity: `lenient` = high-confidence only, `balanced` = standard threshold, `strict` = all potential issues including stylistic.

## Configuration

- **Defaults**: `defaults.json` in repo root (review levels, options, excludes)
- **User overrides**: `~/.claude/argus.json` (personal settings across all projects)
- **Runtime overrides**: CLI flags (`--level`, `--scope`, `--output`, `--external`)

### Smart Scope Detection

When `scope` is `"auto"` (default), checks in order: `src/` → `lib/` → `app/` → `packages/` → `.`

## Orchestrator Behavior (commands/review.md)

The orchestrator spawns ALL analysis agents within a phase in a SINGLE message for parallel execution. After each phase's analysis agents complete, it spawns that phase's validation agent. After all validators complete, it spawns the consolidation agent.

External validators (`agents/external/codex.md`, `agents/external/gemini.md`) are only spawned when `--external` is set AND the CLI tool is detected via version check.

## Core Philosophy

Argus prioritizes **long-term excellence over quick fixes**:
- Recommend the RIGHT solution, not the FAST solution
- Challenge the status quo even for intentional patterns
- Provide migration paths from current to optimal state
- Validation agents must verify with evidence before confirming findings
- Never speculate; only report what can be proven with code evidence
