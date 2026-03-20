---
name: canvas-lms
description: "Canvas LMS integration — list courses, fetch assignments and rubrics, submit homework, check grades, and track completion."
metadata: {"nanobot":{"emoji":"🎓","requires":{"env":["CANVAS_API_TOKEN","CANVAS_BASE_URL"],"bins":["python3"]}}}
---

# Canvas LMS — Course & Assignment Access

Read and write access to Canvas LMS for listing courses, fetching assignment details, and submitting assignments. Includes local SQLite tracking for assignments without a submission button.

## Scripts

- `scripts/canvas_api.py` — Python CLI for Canvas API calls (located in the same directory as this SKILL.md)

## Setup

1. Log in to your Canvas instance in a browser
2. Go to **Account → Settings** (click your profile icon, then Settings)
3. Scroll to **Approved Integrations** and click **+ New Access Token**
4. Name the token, set an optional expiry, and click **Generate Token**
5. Export the token and base URL in your shell or add to `~/.nanobot/.env`:

```
CANVAS_API_TOKEN=your_token_here
CANVAS_BASE_URL=https://yourschool.instructure.com
```

The base URL is whatever appears in your browser when you're logged into Canvas (no trailing slash).

## Step 0: Verify Setup

Before proceeding, verify that CANVAS_API_TOKEN and CANVAS_BASE_URL environment variables are set. Use terminal to run: `echo $CANVAS_API_TOKEN | head -c5` and `echo $CANVAS_BASE_URL`. If either is empty, inform the user they need to configure Canvas API access first.

## Usage

```bash
# Set CANVAS to the script path (relative to this skill's directory)
CANVAS="python3 <SKILL_DIR>/scripts/canvas_api.py"

# List all active courses
$CANVAS list_courses --enrollment-state active

# List assignments for a specific course (ordered by due date)
$CANVAS list_assignments 12345 --order-by due_at

# Get full details for a single assignment (with attachments and links)
$CANVAS get_assignment 12345 67890

# Preview a submission (ALWAYS do this first before submitting)
$CANVAS submit_assignment 12345 67890 --type online_text_entry --body "My answer" --dry-run

# Submit an assignment (after user confirms dry-run)
$CANVAS submit_assignment 12345 67890 --type online_text_entry --body "My answer"

# Submit as URL or file upload
$CANVAS submit_assignment 12345 67890 --type online_url --url "https://myproject.example.com"
$CANVAS submit_assignment 12345 67890 --type online_upload --file /path/to/homework.pdf

# Sync assignments to local tracking database
$CANVAS sync_assignments 12345

# Mark an assignment as done locally (for assignments without a submit button)
$CANVAS mark_done 12345 67890 --notes "Completed in class"

# List pending / completed assignments
$CANVAS list_pending 12345
$CANVAS list_done 12345
```

## Cross-Skill: Todoist Scheduling

After displaying assignments with due dates, propose to the user: "Would you like me to schedule these deadlines in Todoist?" If the user accepts, use the todoist skill to create tasks with the appropriate due dates, durations, and labels.

## Rules

- `list_courses`, `list_assignments`, and `get_assignment` are **read-only**
- `submit_assignment` **MUST** always be called with `--dry-run` first; show the dry-run output to the user and wait for explicit confirmation before running without `--dry-run`
- `mark_done` only writes to the local DB (`~/.nanobot/canvas_assignments.db`) — it never touches Canvas
- Google Assignments (LTI) cannot be submitted via the Canvas API; the command returns the Google URL for manual completion in a browser
- On first use, verify auth by running `$CANVAS list_courses` — if it fails with 401, guide the user through setup
- Run `sync_assignments` before `list_pending` / `list_done` to ensure the local DB is up to date

## Troubleshooting

| Problem | Fix |
|---------|-----|
| 401 Unauthorized | Token invalid or expired — regenerate in Canvas Settings |
| 403 Forbidden | Token lacks permission for this course |
| Empty course list | Try `--enrollment-state active` or omit the flag |
| Wrong institution | Verify `CANVAS_BASE_URL` matches the URL in your browser |
| `submission_type_not_allowed` | The assignment doesn't accept that type; check `allowed_types` |
| `google_assignments` error | Assignment uses Google Assignments LTI — open the provided URL in a browser |
| `not_found` on `mark_done` | Run `sync_assignments` first to populate the local DB |
