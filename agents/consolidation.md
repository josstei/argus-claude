---
id: consolidation
phase: final
depends-on: [validator-1a, validator-2a, validator-3a, validator-4a, validator-5a]
---

# Consolidation Agent

<!-- @include: _shared/philosophy.md -->

## Scope

Merge all validated findings into a final report. Deduplicate, categorize, prioritize, and generate implementation plan.

## Input

- Validated findings from all validation agents (1A-5A)
- External validator findings (Codex, Gemini) if enabled
- Original configuration used

## Process

### Step 1: Filter by Status

Only include findings with status:
- `CONFIRMED` - Verified violations
- `MODIFIED` - Verified with adjustments
- `IMPROVABLE` - Pattern improvement opportunities

Exclude:
- `REJECTED` - False positives
- `NEEDS_REVIEW` - Track separately for human review

### Step 2: Deduplicate

Identify overlapping findings:
- Same file and line from multiple sources
- Same violation type for adjacent lines
- Similar descriptions from different agents

Merge strategy:
- Keep highest severity
- Combine evidence from all sources
- Note all sources that detected it

### Step 3: Categorize

Assign each finding to a category:

| Category | Description |
|----------|-------------|
| `VIOLATION` | Must fix - breaks rules or creates risk |
| `IMPROVEMENT` | Should fix - not broken but suboptimal |
| `EVOLUTION` | Consider - long-term pattern migration |

### Step 4: Prioritize

Sort by:
1. Severity (CRITICAL > HIGH > MEDIUM > LOW > IMPROVEMENT)
2. Category (VIOLATION > IMPROVEMENT > EVOLUTION)
3. File path (group related findings)

### Step 5: Calculate Health Scores

For each domain (0-100 scale):

```
score = 100 - (critical_count * 20) - (high_count * 10) - (medium_count * 5) - (low_count * 2)
```

Domains:
- `architecture` - From agents 1-2
- `codeHygiene` - From agents 3-4
- `naming` - From agent 5
- `quality` - From agents 6-8
- `performance` - From agent 9

Overall = weighted average

### Step 6: Generate Implementation Plan

Group findings into phases:

| Phase | Criteria | Timeframe Hint |
|-------|----------|----------------|
| `phase1Critical` | CRITICAL severity | Immediate |
| `phase2HighPriority` | HIGH severity | Soon |
| `phase3Medium` | MEDIUM severity | Planned |
| `phase4LowAndImprovements` | LOW + IMPROVEMENT | When convenient |

### Step 7: Create Pattern Evolution Roadmap

For IMPROVABLE findings:
- Group by pattern type
- Describe current vs optimal pattern
- Outline migration steps
- Estimate files affected

## Output

Generate final report following `schemas/report.json` structure.

## Quality Checks

Before outputting:
- [ ] All findings have required fields
- [ ] No duplicate findings remain
- [ ] Health scores are within 0-100
- [ ] Implementation phases are ordered by severity
- [ ] All sources properly attributed
