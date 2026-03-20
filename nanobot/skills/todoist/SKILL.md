---
name: todoist
description: "Todoist task management — create tasks, set due dates, schedule study sessions, estimate durations, auto-label, and track time."
metadata: {"nanobot":{"emoji":"✅","requires":{"env":["TODOIST_API_TOKEN"],"bins":["python3"]}}}
---

# Todoist — Intelligent Task Management & Scheduling

Create, manage, and intelligently schedule Todoist tasks. Automatically estimates duration, priority, and labels. Tracks actual task duration to improve future estimates.

## Scripts

- `scripts/todoist_api.py` — Python CLI for Todoist API calls (located in the same directory as this SKILL.md)

## Setup

1. Open [Todoist web app](https://app.todoist.com)
2. Go to **Settings → Integrations → Developer**
3. Copy your **API token**
4. Export in your shell or add to `~/.nanobot/.env`:

```
TODOIST_API_TOKEN=your_token_here
```

## Step 0: Verify Setup

Before proceeding, verify that TODOIST_API_TOKEN is set. Use terminal to run: `echo $TODOIST_API_TOKEN | head -c5`. If empty, inform the user they need to get their API token from https://todoist.com/app/settings/integrations/developer

## Usage

```bash
# Set TODOIST to the script path (relative to this skill's directory)
TODOIST="python3 <SKILL_DIR>/scripts/todoist_api.py"

# List all active tasks
$TODOIST list_tasks

# List tasks with a filter
$TODOIST list_tasks --filter "today | overdue"

# Get a single task's details
$TODOIST get_task 1234567890

# Create a task with all fields (priority 1 = P1/urgent)
$TODOIST create_task --content "Write research paper intro" --due-datetime "2026-03-16T10:00:00Z" --duration 120 --priority 2 --labels "school,writing" --deadline "2026-03-20"

# Create a task with natural language due date
$TODOIST create_task --content "Team meeting" --due-string "tomorrow at 2pm" --duration 60

# Update / complete / delete a task
$TODOIST update_task 1234567890 --duration 90 --priority 1
$TODOIST complete_task 1234567890
$TODOIST delete_task 1234567890

# List projects and labels
$TODOIST list_projects
$TODOIST list_labels

# Get schedule for a date (shows tasks + available gaps)
$TODOIST get_scheduled 2026-03-16
$TODOIST get_scheduled 2026-03-16 --working-hours "10:00-18:00"
```

## Scheduling Workflow

When creating a task, follow this workflow:

1. **Gather info** — extract content, description, and any explicit duration/priority/deadline/labels from the user's request.

2. **Estimate missing fields:**
   - **Duration** — Quick tasks: 15min, medium: 30-60min, complex: 90-120min. Tasks over 120min should be broken into sub-tasks under a dedicated project.
   - **Priority** — P1 (urgent/overdue), P2 (deadline in 2-3 days), P3 (soft deadline), P4 (routine/default).
   - **Labels** — Run `list_labels` first; reuse existing labels. Create new ones if needed.
   - **Duration requires a start time** — do NOT set `--duration` without `--due-datetime`.

3. **Confirm guessed fields** — if you estimated any field, present a summary and ask for confirmation before creating.

4. **Find a time slot** — run `get_scheduled DATE` to see existing tasks and available gaps. Pick a gap that fits. If no gap, suggest the next available date.

5. **Create the task** with all fields set.

See `references/scheduling-workflow.md` for the full detailed workflow.

## Time Tracking

When a user completes a task, ask if the estimated duration was accurate. Save corrections to MEMORY.md (e.g., "Duration accuracy: writing reports — estimated 60min, actually took 90min"). Use these entries to calibrate future estimates.

## Rules

- `list_tasks`, `get_task`, `list_projects`, `list_labels`, `get_scheduled` are **read-only**
- `create_task` **MUST** always include `--duration` — estimate if the user doesn't provide one (but requires `--due-datetime`)
- Tasks over **120 minutes** should be broken into sub-tasks under a dedicated project
- `delete_task` **MUST** be confirmed with the user before execution
- If you estimated duration/priority/labels/deadline, **confirm with the user** before creating
- On first use, verify auth by running `$TODOIST list_projects` — if it fails with 401/403, guide the user through setup
- Working hours default to **08:00–22:00**

## Troubleshooting

| Problem | Fix |
|---------|-----|
| 401 Unauthorized | Token invalid — regenerate in Todoist Settings → Integrations → Developer |
| 403 Forbidden | Token lacks required permissions |
| Empty task list | Check filter syntax or try without `--filter` |
| Rate limit hit | Wait a few minutes; Todoist allows 1000 requests per 15 min |
| `get_scheduled` shows no gaps | Day is fully booked — suggest an alternative date |
| Duration not saved | Ensure `--duration` flag is provided (integer minutes) |
