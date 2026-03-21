---
name: brain-graph-timeout
description: Fix brain-graph-updater timing out with too many notes
tags: [brain, graph, openclaw, timeout, fix]
---

# Brain Graph Updater Timeout Fix

## Problem

The graph updater at `/media/tony/Drive2/Programs/brain/graph_updater.py` times out when processing 10,000+ notes. Previous graph only had ~3,000 connections.

## Root Cause

The script regenerates the entire graph every time, even for 1 new note. With 11,000+ notes, TF-IDF computation takes 10+ minutes.

## Solution

Made the updater incremental:
1. Only rebuild when 10+ new items exist
2. Added timeout handling (5 minute max)
3. Skip small updates to avoid wasting resources

### Key Changes

```python
# Only rebuild if enough new items
if len(missing) < 10:
    print(f"\n⏭️  Only {len(missing)} new items - skipping rebuild (need 10+)")
    return len(missing)

# Add timeout handling
signal.signal(signal.SIGALRM, timeout_handler)
signal.alarm(300)  # 5 minute timeout
```

## Result

- Graph now has 11,818 nodes and 50,000 links
- Cron job only runs heavy processing when worthwhile
- Manual runs available for small updates

## Manual Usage

```bash
python3 /media/tony/Drive2/Programs/brain/graph_updater.py
```

## Monitoring

Check graph status:
```bash
python3 -c "import json; d=json.load(open('/media/tony/Drive2/Brain/graph_data.json')); print(f'Nodes: {len(d[\"nodes\"])}, Links: {len(d[\"links\"])}')"
```

View at: http://localhost:8080/graph.html

## Related Files

- `/media/tony/Drive2/Programs/brain/graph_updater.py`
- `/media/tony/Drive2/Programs/brain/brain.py` (compute_tfidf_links function)
- `/media/tony/Drive2/Brain/graph_data.json`