# Argus

> The all-seeing code reviewer

Multi-agent code quality and architecture review with cross-validation.

## Quick Start

```bash
# Install
/plugin install github:josstei/argus

# Run a review
/argus:review              # Shows confirmation prompt
/argus:review --yes        # Accepts defaults immediately
```

Select a review level and go. No configuration needed.

## Review Levels

| Level | Model | Time | Token Usage | Best For |
|-------|-------|------|-------------|----------|
| **Fast** | Haiku | ~2 min | Low | Quick checks during development |
| **Balanced** | Sonnet | ~5 min | Moderate | Regular code reviews |
| **Comprehensive** | Opus | ~8 min | Higher | PR reviews, releases |

```bash
/argus:review                     # Shows confirmation prompt
/argus:review --level fast        # Skip prompt, use Fast
/argus:review --level balanced    # Skip prompt, use Balanced
/argus:review --yes               # Skip prompt, use defaults
```

## Commands

| Command | Description |
|---------|-------------|
| `/argus:review` | Run code review |
| `/argus:config` | View settings |
| `/argus:help` | Show help |

## Settings

```bash
/argus:config set scope src/       # Always review src/
/argus:config set output markdown  # Always save reports
```

### Config File

Argus reads only the user-level config file:

- **User**: `~/.claude/argus.json` (personal, all projects)

## Review Phases

| Phase | Focus |
|-------|-------|
| Architecture | Boundary violations, layer issues |
| Code Hygiene | Dead code, unused exports |
| Naming | Convention compliance |
| Quality | DI patterns, error handling, resources |
| Optimization | Performance opportunities |

Phases run based on `depth` setting:
- `quick` - Architecture + Hygiene
- `standard` - All except Optimization
- `thorough` - All phases

## External Validators

Enable cross-validation with other LLM CLIs:

```bash
/argus:review --external
```

Requires Codex CLI and/or Gemini CLI installed. Argus auto-detects availability and only enables detected tools.

## Philosophy

Argus prioritizes **long-term excellence over quick fixes**:

- Recommends the RIGHT solution, not the FAST solution
- Identifies patterns that work but could be better
- Provides migration paths from current to optimal state

## Requirements

- Claude Code CLI
- Claude Pro or Max subscription (Sonnet/Opus)
- Optional: Codex CLI, Gemini CLI

## License

MIT
