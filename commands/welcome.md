---
description: Welcome to Argus - getting started guide
---

# Argus Welcome

Display the welcome message and getting started guide.

## Output

Display this message:

```
=====================================
  Argus - The All-Seeing Code Reviewer
=====================================

Quick Start:
  /argus:review              Run review (shows confirmation)
  /argus:review --yes        Run with defaults (no prompt)
  /argus:review --level fast Quick scan with Haiku
  /argus:review --external   Add detected external validators

Review Levels:
  fast          Haiku | ~2 min | Quick scan
  balanced      Sonnet | ~5 min | Standard review
  comprehensive Opus | ~8 min | Deep analysis

Settings:
  /argus:config              View current settings
  /argus:config set scope X  Set default scope
  /argus:config set output X Save reports as markdown/json

Just run /argus:review to get started!
=====================================
```
