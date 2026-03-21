---
name: brain-usage-guide
description: How to use the Brain knowledge system effectively
tags: [brain, knowledge, openclaw]
---

# Brain Usage Guide

## What is Brain?

Personal knowledge base with RAG (Retrieval-Augmented Generation) built on SQLite + QMD search.

## Commands

```bash
# Chat with your notes
python3 /media/tony/Drive2/Programs/brain/brain.py chat "your question"

# Search notes
python3 /media/tony/Drive2/Programs/brain/brain.py search "term"

# Add a note
python3 /media/tony/Drive2/Programs/brain/brain.py note --title "Title" --content "Content" --tags "tag1,tag2"

# Generate flashcards
python3 /media/tony/Drive2/Programs/brain/brain.py flashcard --random

# Summarize a note
python3 /media/tony/Drive2/Programs/brain/brain.py summarize <note-id>

# Multi-source search
python3 /media/tony/Drive2/Programs/brain/brain.py multi "query"

# Hybrid search
python3 /media/tony/Drive2/Programs/brain/brain.py hybrid "query"
```

## URLs

- Web UI: http://localhost:8080/
- Graph: http://localhost:8080/graph.html
- API: http://localhost:8081/

## brain_all - Unified Search

Searches Brain + PDFs + Memory in one command:
```bash
python3 /media/tony/Drive2/Programs/brain_all.py search "question"
```
Web UI: http://localhost:8768/brain_all_gui.html

## Stats (as of March 2026)
- Notes: ~11,893
- Transcripts: 28
- Graph nodes: 11,818
- Graph links: 50,000

## Best Practices

1. Use tags when adding notes
2. Run auto-sync hourly: `python3 /media/tony/Drive2/Programs/brain/auto_sync.py`
3. Regenerate graph after bulk imports
4. Use QMD for search: `qmd search "query" -c notes`

## Common Fixes

- Graph timeout: Use incremental updater (graph_updater.py)
- Search not working: Re-index with `qmd collection add notes /media/tony/Drive2/Brain/notes`
- Missing notes: Run auto_sync.py