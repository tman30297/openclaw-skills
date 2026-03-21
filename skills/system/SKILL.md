---
name: system-check-quick
description: Quick system health check for OpenClaw
tags: [system, openclaw, monitoring]
---

# Quick System Health Check

## One-Liner

```bash
uptime && echo "---" && free -h && echo "---" && df -h / | tail -1 && echo "---" && ps aux | grep -E "(openclaw|ollama|brain)" | grep -v grep | head -5
```

## Full Check

```bash
# System resources
uptime
free -h
df -h /

# Services
ss -tlnp | grep -E ":(8080|8081|8765|8768)"

# Cron jobs
openclaw cron list

# PDF progress
python3 -c "import json; p=json.load(open('/media/tony/Drive2/Programs/pdf_to_text/progress.json')); print(f'PDFs: {len(p[\"text_extracted\"])} extracted, {len(p[\"summarized\"])} summarized')"

# Brain stats
sqlite3 /media/tony/Drive2/Brain/brain.db "SELECT COUNT(*) FROM notes;"

# Graph
python3 -c "import json; d=json.load(open('/media/tony/Drive2/Brain/graph_data.json')); print(f'Graph: {len(d[\"nodes\"])} nodes, {len(d[\"links\"])} links')"
```

## Common Issues to Look For

| Issue | Check | Fix |
|-------|-------|-----|
| Cron job errors | `openclaw cron list \| grep error` | Check model availability |
| PDF stuck | `tail -20 /media/tony/Drive2/Programs/pdf_to_text/process.log` | Reset progress file |
| Service down | `ss -tlnp \| grep <port>` | Restart service |
| Disk full | `df -h /` | Clean up old files |

## URLs

- Brain: http://localhost:8080/
- Graph: http://localhost:8080/graph.html
- Dashboard: http://localhost:8765/
- brain_all: http://localhost:8768/brain_all_gui.html