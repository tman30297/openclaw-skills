---
name: pdf-progress-fix
description: How to fix corrupted PDF progress file that causes processing to fail
tags: [pdf, openclaw, fix]
---

# PDF Progress File Fix

## Problem

The PDF progress file at `/media/tony/Drive2/Programs/pdf_to_text/progress.json` becomes corrupted with the full list of PDFs instead of actual progress counts.

## Symptoms

- `process_batch.py` shows 271/278 extracted but only 79 .txt files exist
- Batch processor keeps re-processing same files
- JSON structure is wrong (contains full file list instead of counts)

## Fix

```python
import os
import json

extracted_dir = "/media/tony/Drive2/Programs/pdf_to_text/extracted"
txt_files = [f.replace(".txt", "") for f in os.listdir(extracted_dir) if f.endswith(".txt")]

progress = {
    "text_extracted": txt_files,
    "summarized": [],
    "errors": [],
    "last_run": None
}

with open("/media/tony/Drive2/Programs/pdf_to_text/progress.json", "w") as f:
    json.dump(progress, f, indent=2)
```

## Verification

```bash
python3 -c "import json; p=json.load(open('/media/tony/Drive2/Programs/pdf_to_text/progress.json')); print(f'Text: {len(p[\"text_extracted\"])}, Summarized: {len(p[\"summarized\"])}, Errors: {len(p[\"errors\"])}')"
```

## Root Cause

The progress file was being written with the entire file list instead of tracking actual extracted files. Need to rebuild from actual .txt files in the extracted directory.

## Related Files

- `/media/tony/Drive2/Programs/pdf_to_text/progress.json`
- `/media/tony/Drive2/Programs/pdf_to_text/extracted/`
- `/media/tony/Drive2/Programs/pdf_to_text/process_batch.py`