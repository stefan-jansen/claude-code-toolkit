---
title: memory-gc
aliases: [/memory-gc]
description: Garbage collection for stale memory entries - identify and clean up obsolete content
allowed-tools: [Read, Edit, Bash, Glob, Write]
argument-hint: "[--auto] [--dry-run]"
---

# Memory Garbage Collection

Systematic cleanup of stale, obsolete, or incorrect memory entries.

**Input**: $ARGUMENTS

## Philosophy: Removal Without Guilt

Memory should reflect CURRENT reality, not history. Remove entries proven wrong, superseded, or obsolete. Archive if historical value exists.

## Modes

- *(no args)*: Interactive — scan, present findings, ask what to do for each stale file
- `--auto`: Non-interactive — archive all stale files automatically
- `--dry-run`: Show what would be cleaned, make no changes

## Constants

- **Memory directory**: `.claude/memory/`
- **Archive directory**: `.claude/work/archives/memory/`
- **Staleness threshold**: 30 days since last validated/updated timestamp

## Process

### 1. Check Prerequisites

Verify `.claude/memory/` directory exists. If not, tell the user:
> No memory directory found at `.claude/memory/`. Run `/memory:memory-update` to create initial memory structure.

### 2. Scan and Classify

For each `.md` file in `.claude/memory/`:

1. Read the file and extract the last validated/updated date using:
   ```bash
   grep -oE "Last (validated|updated).*[0-9]{4}-[0-9]{2}-[0-9]{2}" "$FILE" | tail -1 | grep -oE "[0-9]{4}-[0-9]{2}-[0-9]{2}"
   ```

2. Calculate days since that date (macOS-portable):
   ```bash
   date_epoch=$(date -d "$DATE_STR" +%s 2>/dev/null || date -j -f "%Y-%m-%d" "$DATE_STR" "+%s" 2>/dev/null || echo 0)
   current_epoch=$(date +%s)
   days_since=$(( (current_epoch - date_epoch) / 86400 ))
   ```

3. Classify:
   - **Fresh**: timestamp exists and is <30 days old
   - **Stale**: timestamp exists and is >30 days old
   - **No timestamp**: treat as stale

### 3. Present Findings

Show the user a summary table:

```
| File | Last Validated | Days | Status |
|------|---------------|------|--------|
| project_state.md | 2026-01-15 | 44 | Stale |
| conventions.md | 2026-02-20 | 8 | Fresh |
| decisions.md | N/A | - | No timestamp |
```

If no stale files found, report that all memory is fresh and stop.

### 4. Review Stale Files

**If `--dry-run`**: Stop here. Report what would be cleaned.

**If `--auto`**: Archive all stale files (skip to action execution).

**If interactive (default)**: For each stale file:

1. Read and summarize the file content (first ~20 lines or key sections)
2. Ask the user which action to take:
   - **Keep**: Content is still valid — update the timestamp to today
   - **Archive**: Has historical value but is no longer current — move to archive
   - **Delete**: Incorrect or obsolete — remove entirely (confirm before deleting)
   - **Skip**: Review later — leave unchanged

### 5. Execute Actions

For each file based on the chosen action:

**Keep** — Update the timestamp using the Edit tool:
- If file contains "Last validated:", replace that line with today's date
- If file contains "Last updated:", replace that line with today's date
- If neither exists, add `**Last validated**: YYYY-MM-DD` near the top of the file

**Archive** — Move to the archive directory:
```bash
mkdir -p .claude/work/archives/memory
mv ".claude/memory/FILENAME.md" ".claude/work/archives/memory/FILENAME_$(date +%Y-%m-%d).md"
```

**Delete** — Remove the file (only after explicit user confirmation):
```bash
rm ".claude/memory/FILENAME.md"
```

**Skip** — No action, move to next file.

### 6. Summary

After all actions are complete, report:

```bash
remaining=$(find .claude/memory -name "*.md" -type f | wc -l | tr -d ' ')
total_size=$(du -sh .claude/memory 2>/dev/null | cut -f1)
archived=$(find .claude/work/archives/memory -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')
```

Present the results:
- Files remaining in memory
- Total memory size
- Number of archived entries (if any)

Suggest next steps:
- `/memory:memory-review` to verify state
- `/memory:memory-update` to add new learnings

## Integration

**Called by**: `/status` (warn if >30 days), Manual execution
**Related**: `/memory:memory-review`, `/memory:memory-update`

---

**Plugin**: claude-code-memory v1.0.0
