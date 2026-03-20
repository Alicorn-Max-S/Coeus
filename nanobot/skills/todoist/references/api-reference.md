# Todoist — curl API Reference

**Note:** Todoist migrated from REST API v2 to the unified API v1 in early 2026.

```bash
# List all tasks
curl -s -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  "https://api.todoist.com/api/v1/tasks"

# Filter tasks
curl -s -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  "https://api.todoist.com/api/v1/tasks/filter?query=today"

# Create a task with duration
curl -s -X POST -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"content": "Team meeting", "due_datetime": "2026-03-16T14:00:00Z", "duration": {"amount": 60, "unit": "minute"}, "priority": 3, "labels": ["work"]}' \
  "https://api.todoist.com/api/v1/tasks"

# Complete a task
curl -s -X POST -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  "https://api.todoist.com/api/v1/tasks/TASK_ID/close"

# List projects / labels
curl -s -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  "https://api.todoist.com/api/v1/projects"
curl -s -H "Authorization: Bearer $TODOIST_API_TOKEN" \
  "https://api.todoist.com/api/v1/labels"
```

## v1 API Key Differences from REST v2

| Change | REST v2 | API v1 |
|--------|---------|--------|
| Base URL | `/rest/v2` | `/api/v1` |
| List responses | Bare JSON array | `{"results": [...], "next_cursor": ...}` |
| Task filtering | `GET /tasks?filter=...` | `GET /tasks/filter?query=...` |
| Duration | `"duration": 60, "duration_unit": "minute"` | `"duration": {"amount": 60, "unit": "minute"}` |
| IDs | Numeric | Alphanumeric |
| Priority | Inverted: API 4 = app P1 | Same — `todoist_api.py` converts automatically |
