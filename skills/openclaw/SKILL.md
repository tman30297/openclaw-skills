---
name: openclaw-commands
description: Essential OpenClaw commands and patterns
tags: [openclaw, cli, commands]
---

# OpenClaw Commands

## Gateway

```bash
# Check status
openclaw gateway status

# Start/Stop/Restart
openclaw gateway start
openclaw gateway stop
openclaw gateway restart
```

## Cron Jobs

```bash
# List all jobs
openclaw cron list

# List errors only
openclaw cron list | grep error

# Run a job manually
openclaw cron run <job-id>

# Add a new job
openclaw cron add --name "My Job" --schedule "0 */2 * * *" --message "Run: python3 script.py"

# Update a job
openclaw cron update <job-id> --model "default"
```

## Key Patterns

### Best Practice: Model Selection
- ✅ Use `model: "default"` in cron jobs (uses fallback chain)
- ❌ Don't hardcode specific models like `gemma3:4b`

### Best Practice: Delivery Mode
- Use `delivery.mode: "none"` for isolated sessions
- Avoid `announce` unless channel is configured

### Best Practice: Stagger
- Add `staggerMs: 300000` (5 min) to spread load

## Config Location
- Main config: `~/.openclaw/openclaw.json`
- Memory: `~/.openclaw/workspace/memory/`
- Workspace: `~/.openclaw/workspace/`

## Common Issues & Fixes

| Issue | Command | Fix |
|-------|---------|-----|
| Cron failing | `openclaw cron list \| grep error` | Check model, run manually |
| Gateway down | `openclaw gateway status` | `openclaw gateway restart` |
| Memory not working | Check `memory_search` | Ensure QMD indexed |

## Services to Monitor

| Port | Service |
|------|---------|
| 8080 | Brain Static |
| 8081 | Brain API |
| 8765 | System Dashboard |
| 8768 | brain_all |