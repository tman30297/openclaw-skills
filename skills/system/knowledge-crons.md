---
name: knowledge-system-crons
description: Automated cron jobs for the knowledge system
tags: [cron, automation, knowledge-system]
---

# Knowledge System Cron Jobs

## Overview

Three cron jobs automate the knowledge system maintenance.

## Jobs

### 1. QMD Index Refresh
- **ID:** 912aa19b-cd0d-41ea-adb8-7fbeda21cec2
- **Schedule:** Daily at 4:00 AM
- **Purpose:** Refresh QMD embeddings for all collections

```bash
qmd embed -c memory
qmd embed -c notes
qmd embed -c transcripts
```

### 2. Skills Git Sync
- **ID:** d774c679-6dcc-48f3-8aba-6b82ef400961
- **Schedule:** Daily at 5:00 AM
- **Purpose:** Auto-push skills to GitHub

```bash
cd ~/openclaw-skills
git add .
git commit -m "Auto-update: $(date '+%Y-%m-%d %H:%M')"
git push origin master
```

### 3. QMD Diagnostic
- **ID:** 2e4f11e1-5f2e-48f1-bc2e-00ebcca7e4ab
- **Schedule:** Weekly Sunday at 6:00 AM
- **Purpose:** Check search system health

```bash
/home/tony/.openclaw/workspace/bin/qmd-diagnostic.sh
```

## Management

### View Jobs
```bash
openclaw cron list | grep -E "(QMD|Skill|Diagnostic)"
```

### Run Manually
```bash
openclaw cron run 912aa19b-cd0d-41ea-adb8-7fbeda21cec2  # QMD Index
openclaw cron run d774c679-6dcc-48f3-8aba-6b82ef400961  # Skills Git Sync
openclaw cron run 2e4f11e1-5f2e-48f1-bc2e-00ebcca7e4ab  # QMD Diagnostic
```

### Disable/Enable
```bash
openclaw cron update <job-id> --enabled false
openclaw cron update <job-id> --enabled true
```

## Related Files
- Script: `/home/tony/.openclaw/workspace/bin/qmd-index-hook.sh`
- Script: `/home/tony/.openclaw/workspace/bin/qmd-diagnostic.sh`
- Skills repo: `~/openclaw-skills/`