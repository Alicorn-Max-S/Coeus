# Todoist — Detailed Scheduling Workflow

## Step 1: Gather Info

Extract from the user's request: content, description, and any explicitly provided duration, priority, deadline, or labels.

## Step 2: Resolve Relative Time References

If the user references an event by name to anchor timing (e.g., "after track", "before dinner"):

1. Ask the user what time that event starts/ends — e.g., "What time does [event] end?"
2. Use the resolved time as a hard constraint when finding a time slot.

## Step 3: Estimate Missing Fields

**Duration** — Estimate from the task title/description:
- Quick tasks (reply to email, review a doc): **15 min**
- Medium tasks (short report, meeting, short assignment): **30–60 min**
- Complex tasks (coding project, research, essay): **90–120 min**
- Deep work (major milestone, exam prep): **120 min** (break into sub-tasks if longer)

**IMPORTANT:** Duration requires a specific start time (`--due-datetime`). Do NOT set `--duration` on tasks with only a date. If the user provides a duration but no start time, find a time slot first.

Check MEMORY.md for past duration corrections to calibrate estimates.

**Breaking Up Large Tasks** — If a task would take more than 120 minutes, break it into smaller tasks under a dedicated project:
1. Create a project: "Essay — Climate Change"
2. Create individual tasks: "Identify sources" (60 min), "Write outline" (30 min), "Write draft" (90 min), "Revise" (90 min)

**Priority** — P1–P4 scale (P1 = most urgent):
- **P1**: Overdue, due today with hard deadline, explicitly urgent
- **P2**: Important with deadline in 2–3 days, assignments due this week
- **P3**: Soft/distant deadline, recurring responsibilities
- **P4**: Routine tasks, no urgency (default)

**Labels** — Run `list_labels` first. Reuse existing labels. Infer from context:
- Academic → "school", "homework", "study"
- Work → "work", "meeting"
- Personal → "personal", "errands"

If no matching label exists, include the new name in `--labels` and Todoist creates it automatically.

**Projects** — Run `list_projects` to see existing projects. Assign to the most relevant one.

**Deadline** — Only set if the user explicitly mentions a deadline.

## Step 4: Confirm Guessed Fields

If you estimated ANY field, present a summary before creating:

```
Here's what I'll create:
- Task: "Write research paper intro"
- Duration: 120 min (estimated — complex writing task)
- Priority: P2/High (estimated — academic deadline approaching)
- Labels: school (estimated — matches your existing label)
- Scheduled: 2026-03-16 10:00–12:00 (first available 2h gap)

Want me to adjust anything before creating this task?
```

## Step 5: Find a Time Slot

1. Run `get_scheduled DATE` for the target date.
2. Find a gap in `available_gaps` that fits the duration. Prefer earlier gaps for deep work, later for lighter tasks.
3. If a time anchor was resolved in Step 2, only consider gaps that respect it.
4. If no gap is large enough, inform the user and suggest the next date with availability.

## Step 6: Create the Task

```bash
$TODOIST create_task --content "..." --due-datetime "..." --duration N --priority P --labels "..." --deadline "..."
```
