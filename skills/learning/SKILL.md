---
name: learned-md-pattern
description: Self-learning pattern - capture and consolidate learnings
tags: [learning, memory, self-improvement]
---

# LEARNED.md Pattern

## Purpose

Capture key learnings after mistakes or discoveries so they don't get lost. Consolidate periodically.

## Setup

```bash
# Create .learnings folder
mkdir -p ~/.openclaw/workspace/.learnings

# Create LEARNED.md
cat > ~/.openclaw/workspace/.learnings/LEARNED.md << 'EOF'
# LEARNED.md - What I Learned Today

## [Date]

### Topic
- What was learned
- Why it matters
- How to apply it
EOF
```

## Usage

When you make a mistake or learn something important:

1. **Capture immediately** - Write to today's memory file
2. **Consolidate** - Add key point to LEARNED.md
3. **Convert to skill** - If it's a repeatable fix, create a skill

## Example

```markdown
## March 21, 2026

### Cron Model Errors
- Cron jobs failed with "model not found" because hardcoded model names
- Fix: use model: "default" to enable fallback chain
- Applied to: PDF Summarizer, Research Aggregator, brain-graph-updater
```

## Consolidation

Periodically (weekly), review recent memory files and LEARNED.md to:
- Identify patterns
- Convert useful items to skills
- Update MEMORY.md with long-term learnings

## Current Status

- Location: ~/.openclaw/workspace/.learnings/LEARNED.md
- Updated: March 21, 2026
- Topics: Skills system, memory, fixes applied