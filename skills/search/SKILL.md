---
name: vector-index-tuning
description: Optimize QMD vector search for better retrieval accuracy
tags: [qmd, search, optimization, memory]
---

# Vector Index Tuning for QMD

## Overview

Optimize our QMD (QMD) search system for better retrieval. Our current setup uses hash embeddings + BM25 hybrid. This skill provides tuning strategies.

## Current Setup

```
Collections:
- notes: 61 files (Brain notes)
- transcripts: 34 files
- memory: 4 files (daily memory)
- main, longterm, brain, brain-notes (empty)
```

## Tuning Strategies

### 1. Chunk Size Optimization

**Problem:** Too small = lose context. Too large = noisy.

**Current:** Default QMD chunking

**Tune:** Adjust chunk size in qmd config:
```bash
qmd config --set chunk_size 512  # or 1024, 2048
```

### 2. Re-ranking Configuration

**Problem:** BM25 + embeddings may rank differently.

**Current:** RRF (Reciprocal Rank Fusion) combining both

**Tune:** Adjust weights in config:
```toml
[search]
bm25_weight = 0.6    # increase for keyword precision
embedding_weight = 0.4  # increase for semantic recall
```

### 3. Collection-Specific Tuning

**Notes (technical content):**
```toml
[search]
bm25_weight = 0.7   # more keyword matching
min_score = 0.01
```

**Memory (conversational):**
```toml
[search]
bm25_weight = 0.4   # more semantic
embedding_weight = 0.6
```

### 4. Re-index Trigger

When to rebuild index:
- After bulk imports (50+ new files)
- When search quality degrades
- Monthly maintenance

```bash
# Force re-index
qmd embed -c notes --rebuild
qmd embed -c memory --rebuild
```

### 5. Hybrid Search Tuning

**Current command:**
```bash
qmd hybrid "query"
```

**Better queries:**
```bash
# Use quotes for exact phrase
qmd search '"specific phrase"' -c notes

# Use -c for specific collection
qmd search "error" -c memory
qmd search "fix" -c notes
```

## Diagnostics

### Check Index Health

```bash
# List all collections
qmd collection list

# Check specific collection
qmd collection info notes

# Test search quality
qmd search "python" -c notes | head -10
qmd vsearch "programming" -c notes | head -10
```

### Identify Issues

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| No results for common terms | BM25 not indexed | `qmd collection add` |
| Irrelevant results | Weight imbalance | Adjust weights |
| Missing recent items | Embeddings stale | `qmd embed -c <collection>` |
| Slow searches | Too many files | Chunk larger |

## Our Specific Config

Current `.config/ms/config.toml`:
```toml
[search]
bm25_weight = 0.5
embedding_backend = "hash"  # fast, deterministic
embedding_dims = 384
semantic_weight = 0.5
use_embeddings = true
```

## Optimization Plan

1. **Weekly:** Re-embed collections
2. **Monthly:** Tune weights based on query success
3. **Quarterly:** Review chunk sizes

## Commands Reference

```bash
# Search (BM25 - works out of box)
qmd search "query" -c <collection>

# Vector search (requires downloading embedding model ~1.2GB)
qmd vsearch "query" -c <collection>
# May fail if model download fails - use search instead

# Hybrid query (requires embeddings)
qmd query "query" -c <collection>
# Falls back gracefully if embeddings not available

# Index management
qmd collection list
qmd collection add <name> <path>
qmd embed -c <collection>
qmd embed -c <collection> --rebuild

# Config
qmd config --list
qmd config <key> <value>
```

## Current Known Issues

- `qmd query` and `qmd vsearch` try to download embedding model (1.2GB) which may fail
- **Workaround:** Use `qmd search` for BM25-only which works reliably
- Our current setup: hash embeddings (fast) + BM25 (reliable)