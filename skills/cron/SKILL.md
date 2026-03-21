---
name: cron-model-errors
description: Fix cron jobs failing with "model not found" errors
tags: [cron, openclaw, ollama, fix]
---

# Cron Job Model Errors Fix

## Problem

Cron jobs fail with errors like:
- `ollama/gemma3:4b: model not found`
- `ollama/qwen3:8b: model not found`
- `ollama/llama3.2:3b: model not found`

## Root Cause

Cron jobs hardcode specific model names instead of using the default fallback chain. When the model isn't downloaded, the job fails.

## Solution

Change the model in the cron job to use `default` instead of a specific model. This allows the system to use the fallback chain.

### Example

```bash
# Before (fails if model not downloaded)
openclaw cron update <job-id> --model "gemma3:4b"

# After (uses fallback chain)
openclaw cron update <job-id> --model "default"
```

## Verify Models

```bash
ollama list
```

If models are missing, pull them:
```bash
ollama pull gemma3:4b
ollama pull llama3.2:3b
ollama pull qwen3:8b
```

## Jobs That Had This Issue

- Self-improvement session
- PDF Summarizer
- Research Aggregator
- brain-graph-updater

## Prevention

Always use `model: "default"` in cron job configurations. This ensures fallback to working models if primary isn't available.

## Related

- OpenClaw cron jobs: `openclaw cron list`
- Check errors: `openclaw cron list | grep error`