# Advanced Configuration

This guide covers advanced Argus configuration for power users.

## Review Levels

Argus uses a simple 3-tier system:

| Level | Model | Depth | Token Usage | Best For |
|-------|-------|-------|-------------|----------|
| **Fast** | Haiku | quick | Low | Quick checks during development |
| **Balanced** | Sonnet | standard | Moderate | Regular code reviews |
| **Comprehensive** | Opus | thorough | Higher | PR reviews, releases |

When you run `/argus:review`, you can select a level or accept the default (Balanced).

## Global Settings

Settings that apply across all reviews:

| Setting | Description | Default |
|---------|-------------|---------|
| `scope` | Directory to analyze | auto |
| `output` | Output format | inline |

```bash
/argus:config set scope src/       # Always review src/
/argus:config set output markdown  # Always save reports
```

## Depth Explained

Each depth level runs different analysis phases:

| Depth | Phases Run | Agents |
|-------|------------|--------|
| `quick` | Architecture, Hygiene | 4 |
| `standard` | + Naming, Quality | 8 |
| `thorough` | + Optimization | 9 |

### Phase Details

| Phase | Agents | Focus |
|-------|--------|-------|
| Architecture | 01, 02 | Structure, separation of concerns |
| Hygiene | 03, 04 | Dead code, dependencies |
| Naming | 05 | Naming conventions |
| Quality | 06, 07, 08 | DI patterns, error handling, lifecycle |
| Optimization | 09 | Performance opportunities |

## External Validators

### Codex CLI Setup

1. Install the Codex CLI from OpenAI
2. Authenticate: `codex auth login`
3. Verify: `codex --version`
4. Enable with `--external` flag

### Gemini CLI Setup

1. Install the Gemini CLI from Google
2. Authenticate: `gemini auth login`
3. Verify: `gemini --version`
4. Enable with `--external` flag

### Auto-Detection

When running with `--external`, Argus checks availability and only enables detected tools:

```bash
codex --version 2>/dev/null
gemini --version 2>/dev/null
```

If not found, Argus continues without that validator and displays a warning.

### Permissions

Argus auto-approves bash commands for external tools via `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["Bash(codex:*)", "Bash(gemini:*)"]
  }
}
```

## User Configuration

Argus reads only the user-level config file.

Create or update `~/.claude/argus.json`:

```json
{
  "scope": "auto",
  "output": "markdown"
}
```

## Smart Scope Detection

When `scope` is `"auto"` (default), Argus detects:

1. `src/` exists -> use `src/`
2. `lib/` exists -> use `lib/`
3. `app/` exists -> use `app/`
4. `packages/` exists -> use `packages/` (monorepo)
5. Fall back to `.` (current directory)

Override with: `/argus:config set scope lib/`

## Excluded Patterns

Argus always excludes:

- `node_modules/`
- `dist/`
- `build/`
- `.git/`
- `coverage/`
- `*.min.js`
- `*.bundle.js`

## Command-Line Overrides

Override settings for a single run:

```bash
/argus:review --level fast             # Use Fast level
/argus:review --scope lib/             # Override scope
/argus:review --output json            # Override output
/argus:review --yes                    # Accept defaults, no prompt
/argus:review --level balanced --output markdown
```

## Output Formats

| Format | Behavior | Best For |
|--------|----------|----------|
| `inline` | Display in terminal | Interactive use |
| `markdown` | Save to `.claude/argus/reports/` | PR reviews |
| `json` | Save to `.claude/argus/reports/` | CI/CD integration |

## Strictness Levels

| Level | Behavior |
|-------|----------|
| `lenient` | Only high-confidence issues |
| `balanced` | Standard threshold |
| `strict` | All potential issues, including stylistic |

## Troubleshooting

### "Model not available"

Your Claude subscription may not include the requested model:
- Haiku: Free tier
- Sonnet: Pro subscription
- Opus: Max subscription

### "External validator not found"

The CLI tool isn't installed or not in PATH:
```bash
codex --version
gemini --version
```

### "Scope directory not found"

The configured scope doesn't exist. Reset to auto:
```bash
/argus:config set scope auto
```
