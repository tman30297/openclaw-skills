# OpenClaw AI Assistant - Knowledge System Build

A complete guide to the skill-based knowledge system built for OpenClaw, inspired by modern AI agent patterns.

---

## Table of Contents

1. [Overview](#overview)
2. [What We Built](#what-we-built)
3. [How It Works](#how-it-works)
4. [Components](#components)
5. [How to Build It](#how-to-build-it)
6. [How to Use It](#how-to-use-it)
7. [Commands Reference](#commands-reference)
8. [Current Status](#current-status)

---

## Overview

We built a local-first skill management system that:

- **Stores operational knowledge** as reusable skills (SKILL.md files)
- **Provides fast search** via QMD (BM25 + hash embeddings)
- **Learns from mistakes** via LEARNED.md pattern
- **Auto-indexes memory** for immediate searchability
- **Forces AI to check skills** before answering

---

## What We Built

### 1. ms (Meta Skill) - Skill Management

**What:** Local-first skill management platform (Rust CLI)
**Why:** Organize operational knowledge into searchable skills
**Location:** `~/openclaw-skills/`

### 2. QMD Integration - Search

**What:** Hybrid search (BM25 + hash embeddings)
**Why:** Fast, reliable search without external dependencies
**Collections:** notes (61), transcripts (34), memory (4)

### 3. Memory Auto-Index Hook

**What:** Script that indexes memory files after every write
**Why:** New memories are immediately searchable
**Location:** `~/bin/qmd-index-hook.sh`

### 4. CLAUDE.md Pattern

**What:** Rule forcing AI to search skills before answering
**Why:** Prevents hallucination, uses documented solutions
**Location:** `~/.openclaw/workspace/AGENTS.md`

### 5. LEARNED.md Pattern

**What:** Consolidate key learnings after mistakes
**Why:** Prevents repeating the same errors
**Location:** `~/.openclaw/workspace/.learnings/LEARNED.md`

### 6. Vector Index Tuning

**What:** Optimize QMD search for better retrieval
**Why:** Improve search accuracy over time
**Location:** Skill in `~/openclaw-skills/`

---

## How It Works

```
User Question
       │
       ▼
┌──────────────────┐
│  AGENTS.md Rule  │  ← Forces skill search
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ ms search        │  ← Searches skill library
│ qmd search       │  ← Searches memory
└────────┬─────────┘
         │
         ▼
    Found Answer?
         │
    ┌────┴────┐
    │         │
   Yes       No
    │         │
    ▼         ▼
  Use it   Fall back
           to general
           knowledge
```

### Data Flow

```
Writing to memory
       │
       ▼
memory/YYYY-MM-DD.md
       │
       ▼
qmd-index-hook.sh (auto)
       │
       ▼
QMD index updated (BM25)
       │
       ▼
qmd embed (embeddings)
       │
       ▼
Immediately searchable
```

---

## Components

### Directory Structure

```
~/openclaw-skills/          # Main skill repository
├── .ms/                    # ms database
│   ├── ms.db              # SQLite storage
│   ├── index/             # Search index
│   └── config.toml        # Configuration
├── skills/                 # All skills (SKILL.md)
│   ├── pdf/SKILL.md
│   ├── cron/SKILL.md
│   ├── brain/SKILL.md
│   ├── system/SKILL.md
│   ├── ai/SKILL.md
│   ├── openclaw/SKILL.md
│   ├── memory-indexing/SKILL.md
│   ├── process/SKILL.md
│   ├── learning/SKILL.md
│   └── search/SKILL.md
└── .git/                   # Git for sync

~/.openclaw/workspace/
├── AGENTS.md              # Has skill search rule
├── .learnings/
│   └── LEARNED.md         # Consolidated learnings
├── bin/
│   ├── qmd-index-hook.sh # Auto-index memory
│   └── qmd-diagnostic.sh # Check search health
└── memory/                # Daily memory files
    └── YYYY-MM-DD.md
```

### Collections (QMD)

| Collection | Files | Purpose |
|------------|-------|---------|
| notes | 61 | Brain notes |
| transcripts | 34 | Video transcripts |
| memory | 4 | Daily memory |
| main | 0 | (reserved) |
| longterm | 0 | (reserved) |

---

## How to Build It

### Step 1: Install ms

```bash
# Download binary
cd /tmp
curl -LO https://github.com/dicklesworthstone/meta_skill/releases/download/v0.1.0/ms-0.1.0-x86_64-unknown-linux-gnu.tar.gz
tar -xzf ms-*.tar.gz

# Install to user bin
cp ms ~/.local/bin/ms
chmod +x ~/.local/bin/ms
ms --version
```

### Step 2: Initialize Skill Repository

```bash
mkdir -p ~/openclaw-skills
cd ~/openclaw-skills
ms init
```

### Step 3: Configure

```bash
# Edit .ms/config.toml
cat > ~/.openclaw-skills/.ms/config.toml << 'EOF'
[search]
bm25_weight = 0.5
embedding_backend = "hash"
embedding_dims = 384
semantic_weight = 0.5
use_embeddings = true

[skill_paths]
local = []
project = ["./skills"]
EOF
```

### Step 4: Create Skills

Create `SKILL.md` files in `~/openclaw-skills/skills/`:
```markdown
---
name: skill-name
description: What this skill helps with
tags: [tag1, tag2]
---

# Skill Name

## What it does
...

## How to use
...
```

### Step 5: Index Skills

```bash
cd ~/openclaw-skills
ms index
```

### Step 6: Add to AGENTS.md

Add skill search requirement to `~/.openclaw/workspace/AGENTS.md`:
```markdown
## 🔍 Skill & Knowledge Search

Before answering questions about these topics, you MUST search first:

- **System fixes:** Run `cd ~/openclaw-skills && ms search "[topic]"`
- **Memory:** Run `qmd search "[query]" -c memory`
- **Brain notes:** Run `qmd search "[query]" -c notes`
```

### Step 7: Set Up Auto-Index Hook

```bash
# Create hook
cat > ~/bin/qmd-index-hook.sh << 'EOF'
#!/bin/bash
set -e
qmd collection add memory /home/tony/.openclaw/workspace/memory 2>/dev/null || true
qmd embed -c memory 2>/dev/null || true
echo "Memory indexed"
EOF
chmod +x ~/bin/qmd-index-hook.sh
```

### Step 8: Create LEARNED.md

```bash
mkdir -p ~/.openclaw/workspace/.learnings
cat > ~/.openclaw/workspace/.learnings/LEARNED.md << 'EOF'
# LEARNED.md - What I Learned

## [Date]

### Topic
- What was learned
- Why it matters
EOF
```

---

## How to Use It

### For the AI (me)

When you ask a question, I must:
1. Check AGENTS.md rule
2. Run `ms search "[topic]"` for system fixes
3. Run `qmd search "[query]" -c memory` for memory
4. Use found solutions instead of guessing

### For You (Manual)

```bash
# Search skills
cd ~/openclaw-skills
ms search "pdf"
ms search "cron"

# Get suggestions
ms suggest

# Show a skill
ms show skill-name

# Search memory
qmd search "cron" -c memory
qmd search "python" -c notes

# Check system
~/bin/qmd-diagnostic.sh
```

---

## Commands Reference

### ms Commands

| Command | What it does |
|---------|-------------|
| `ms init` | Initialize skill repository |
| `ms index` | Index all SKILL.md files |
| `ms search "query"` | Search skills |
| `ms suggest` | Get context-aware suggestions |
| `ms show <skill>` | View a specific skill |
| `ms config` | View/set configuration |

### QMD Commands

| Command | What it does |
|---------|-------------|
| `qmd collection list` | List all collections |
| `qmd search "query" -c <collection>` | BM25 search |
| `qmd embed -c <collection>` | Refresh embeddings |
| `qmd status` | Check index health |

### Custom Scripts

| Script | What it does |
|--------|-------------|
| `~/bin/qmd-index-hook.sh` | Auto-index memory after writes |
| `~/bin/qmd-diagnostic.sh` | Check search system health |

### Cron Jobs (Automated)

The system includes automated cron jobs for maintenance:

| Job | Schedule | Purpose |
|-----|----------|---------|
| QMD Index Refresh | Daily 4:00 AM | Refresh embeddings for memory, notes, transcripts |
| Skills Git Sync | Daily 5:00 AM | Auto-push skills to GitHub |
| QMD Diagnostic | Weekly Sunday 6:00 AM | Check search health |

View cron jobs:
```bash
openclaw cron list | grep -E "(QMD|Skill)"
```

---

## Current Status (March 2026)

### Skills: 10
- pdf-progress-fix
- cron-model-errors
- brain-graph-timeout
- system-check-quick
- ai-improvement-areas
- openclaw-commands
- qmd-auto-index
- claude-md-pattern
- learned-md-pattern
- vector-index-tuning

### QMD Collections
- notes: 61 files
- transcripts: 34 files
- memory: 4 files
- Total: 99 indexed, 869 vectors

### Cron Jobs (Automated)
- QMD Index Refresh (daily 4AM)
- Skills Git Sync (daily 5AM)
- QMD Diagnostic (weekly Sunday 6AM)

### Git Ready
```bash
cd ~/openclaw-skills
git add .
git commit -m "Add skills"
git push
```

---

## Inspiration

Built based on patterns from:
- [changeflowhq/skills](https://github.com/changeflowhq/skills) - QMD integration
- [dicklesworthstone/meta_skill](https://github.com/dicklesworthstone/meta_skill) - ms CLI
- [wshobson/agents](https://github.com/wshobson/agents) - Vector tuning

---

*Created: March 21, 2026*
*For: Tony's OpenClaw AI Assistant*