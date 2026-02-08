---
description: Show Argus help and common workflows
---

# Argus Help

Display help information based on arguments.

## Arguments: $ARGUMENTS

| Argument | Shows |
|----------|-------|
| (empty) | Command overview |
| `workflows` | Common workflow examples |
| `config` | Configuration options |

---

## Output

### Default (no argument)

```
Argus Commands
==============

Review:
  /argus:review              Run code review
  /argus:review --level X    Use level (fast/balanced/comprehensive)
  /argus:review --scope X    Review specific directory
  /argus:review --output X   Set output (inline/markdown/json)
  /argus:review --external   Enable external validators (detected tools only)
  /argus:review --yes        Accept defaults, no prompt

Config:
  /argus:config              View settings
  /argus:config set scope X  Set default scope
  /argus:config set output X Set default output

Other:
  /argus:help                This help
  /argus:help workflows      Common workflow examples
  /argus:welcome             Show welcome message
```

### workflows

```
Common Workflows
================

Quick check before commit:
  /argus:review --level fast

Full review for PR:
  /argus:review --level comprehensive

Focus on specific area:
  /argus:review --scope features/auth/

Accept defaults immediately:
  /argus:review --yes

Save report to file:
  /argus:review --output markdown

Cross-validate with external tools (detected only):
  /argus:review --external
```

### config

```
Configuration Options
=====================

Settings:
  scope       auto, src/, lib/, path  (default: auto)
  output      inline, markdown, json  (default: inline)

Review Levels:
  fast          Haiku | ~2 min | Quick scan
  balanced      Sonnet | ~5 min | Standard review (default)
  comprehensive Opus | ~8 min | Deep analysis

Examples:
  /argus:config set scope src/
  /argus:config set output markdown

Config file:
  User: ~/.claude/argus.json (personal)
```
