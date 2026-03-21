---
name: qmd-auto-index
description: Auto-index memory files in QMD after every write
tags: [qmd, memory, indexing]
---

# QMD Auto-Index Hook

## Purpose

Automatically index memory files in QMD after every write so they're immediately searchable.

## Setup

```bash
# Create the hook script
cat > /home/tony/.openclaw/workspace/bin/qmd-index-hook.sh << 'EOF'
#!/bin/bash
set -e
qmd collection add memory /home/tony/.openclaw/workspace/memory 2>/dev/null || true
qmd embed -c memory 2>/dev/null || true
echo "Memory indexed"
EOF
chmod +x /home/tony/.openclaw/workspace/bin/qmd-index-hook.sh
```

## Usage

After any memory write, call:
```bash
/home/tony/.openclaw/workspace/bin/qmd-index-hook.sh
```

Or manually search:
```bash
qmd search "query" -c memory
qmd hybrid "query"
```

## Verification

```bash
qmd collection list | grep memory
```

Should show Files: N (N = number of memory files)

## Current Status (March 2026)

- Collection exists and indexed
- Memory files: 2026-03-17 through 2026-03-21
- Embeddings: Up to date