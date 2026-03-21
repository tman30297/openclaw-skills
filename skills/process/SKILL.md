---
name: claude-md-pattern
description: Force AI to check skills/memory before answering
tags: [claude, prompt, memory]
---

# CLAUDE.md Pattern

## What it does

Add instructions to a CLAUDE.md file that force the AI to search relevant knowledge bases before answering questions.

## Implementation

In any directory with a CLAUDE.md, add this pattern:

```markdown
# Instructions

Before answering any question about [topic], you MUST:
1. First run `ms search "[relevant term]"` to check local skills
2. Then run `qmd search "[query]" -c memory` to check memory
3. Only then provide your answer

Failure to search first may result in incorrect or incomplete answers.
```

## For our system

We could add to `~/.openclaw/workspace/AGENTS.md`:

```markdown
## Skill Search Rule

Before answering questions about:
- System fixes: Run `cd ~/openclaw-skills && ms search "[topic]"`
- Memory: Run `qmd search "[query]" -c memory`
- Brain: Run `qmd search "[query]" -c notes`
```

## Example

User: "How do I fix a cron job error?"
AI must first: 
1. `ms search "cron error"`
2. `qmd search "cron job" -c memory`
3. Then answer based on found skills

## Current Status

This pattern can be added to our AGENTS.md to improve accuracy.