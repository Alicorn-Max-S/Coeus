# Canvas LMS — Output Formats

**list_courses** returns:
```json
[{"id": 12345, "name": "Intro to CS", "course_code": "CS101", "workflow_state": "available", "start_at": "...", "end_at": "..."}]
```

**list_assignments** returns:
```json
[{"id": 67890, "name": "Homework 1", "due_at": "2025-02-15T23:59:00Z", "points_possible": 100, "submission_types": ["online_upload"], "html_url": "...", "description": "...", "course_id": 12345}]
```

Note: Assignment descriptions are truncated to 500 characters. The `html_url` field links to the full assignment page in Canvas.

**get_assignment** returns:
```json
{
  "id": 67890,
  "name": "Final Project",
  "course_id": 12345,
  "description": "Full HTML description text (not truncated)...",
  "due_at": "2025-05-01T23:59:00Z",
  "points_possible": 100,
  "submission_types": ["online_upload"],
  "html_url": "https://yourschool.instructure.com/courses/12345/assignments/67890",
  "attachments": [
    {"display_name": "rubric.pdf", "url": "https://...", "content_type": "application/pdf", "size": 204800}
  ],
  "external_tool_url": "",
  "google_assignments": false,
  "locked_for_user": false,
  "lock_explanation": ""
}
```

**submit_assignment --dry-run** returns:
```json
{
  "dry_run": true,
  "assignment_name": "Homework 3",
  "course_id": "12345",
  "assignment_id": "67890",
  "submission_type": "online_text_entry",
  "body": "My completed answer...",
  "url": null,
  "file": null,
  "file_size_bytes": null,
  "html_url": "https://yourschool.instructure.com/courses/12345/assignments/67890"
}
```

**submit_assignment** (actual) returns:
```json
{"success": true, "submission_id": 111222, "submitted_at": "2025-04-30T18:00:00Z", "workflow_state": "submitted", "submission_type": "online_text_entry"}
```

**sync_assignments** returns:
```json
{"synced": 15, "course_id": 12345, "synced_at": "2025-04-30T18:00:00Z"}
```

**mark_done** returns:
```json
{"success": true, "assignment_id": 67890, "done_at": "2025-04-30T18:00:00Z"}
```

**list_pending / list_done** returns:
```json
[{"id": 67890, "course_id": 12345, "name": "Homework 1", "due_at": "...", "points_possible": 100, "submission_types": ["online_upload"], "html_url": "...", "last_synced": "...", "local_done": 0, "done_at": null, "done_notes": null}]
```
