---
description: View and configure Argus settings
---

# Argus Configuration

View review levels and manage global settings.

## Arguments: $ARGUMENTS

Parse the arguments to determine the action:

| Command | Description |
|---------|-------------|
| (empty) | Show review levels and current settings |
| `set scope <path>` | Set default scope directory |
| `set output <format>` | Set default output format |

---

## Review Levels

Argus has three built-in review levels:

| Level | Model | Depth | Description |
|-------|-------|-------|-------------|
| **Fast** | Haiku | quick | Quick scan (~2 min, low tokens) |
| **Balanced** | Sonnet | standard | Standard review (~5 min, moderate tokens) |
| **Comprehensive** | Opus | thorough | Deep analysis (~8 min, higher tokens) |

---

## Implementation

### Step 1: Determine Action

Parse `$ARGUMENTS`:

```
- Empty → Show current state
- "set scope <path>" → Set scope
- "set output <format>" → Set output
```

### Step 2: Execute Action

#### Action: (empty) - Show Configuration

1. Load `defaults.json` to get review levels
2. Check for scope/output overrides in `~/.claude/argus.json`
3. Detect current scope if set to "auto"
4. Display:

```
Argus Configuration
===================

Review Levels:
  fast          Haiku | ~2 min
  balanced      Sonnet | ~5 min (default)
  comprehensive Opus | ~8 min

Settings:
  scope:  {scope_value} {if auto: (currently: detected_path)}
  output: {output_value}

Change settings:
  /argus:config set scope lib/
  /argus:config set output markdown
```

#### Action: `set scope <path>`

1. Validate the path is "auto" or exists as a directory
2. Write to `~/.claude/argus.json`:
   ```json
   { "scope": "lib/" }
   ```
3. Display confirmation:
   ```
   Set scope = lib/
   ```

Valid values: `auto`, or any valid directory path

#### Action: `set output <format>`

1. Validate format is one of: `inline`, `markdown`, `json`
2. Write to `~/.claude/argus.json`:
   ```json
   { "output": "markdown" }
   ```
3. Display confirmation:
   ```
   Set output = markdown
   ```

---

## Config File Format

Argus reads and writes only the user-level config file.

`~/.claude/argus.json` (user-level, personal):

```json
{
  "scope": "auto",
  "output": "markdown"
}
```
