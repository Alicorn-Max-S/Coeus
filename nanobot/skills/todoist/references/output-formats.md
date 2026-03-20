# Todoist — Output Formats

**list_tasks** returns:
```json
[{
  "id": "1234567890",
  "content": "Write report",
  "description": "Q1 summary",
  "due": {"date": "2026-03-16", "datetime": "2026-03-16T10:00:00Z", "string": "Mar 16 10:00 AM", "timezone": "America/New_York", "is_recurring": false},
  "duration": {"amount": 60, "unit": "minute"},
  "priority": 2,
  "project_id": "2345678",
  "labels": ["work"],
  "url": "https://todoist.com/showTask?id=1234567890"
}]
```

**get_scheduled** returns:
```json
{
  "date": "2026-03-16",
  "working_hours": {"start": "08:00", "end": "22:00"},
  "scheduled_tasks": [
    {"id": "123", "content": "Team meeting", "start": "09:00", "end": "10:00", "duration_minutes": 60, "priority": 2, "labels": ["work"]}
  ],
  "unscheduled_tasks": [
    {"id": "789", "content": "Buy groceries", "due_date": "2026-03-16"}
  ],
  "available_gaps": [
    {"start": "08:00", "end": "09:00", "duration_minutes": 60},
    {"start": "10:00", "end": "22:00", "duration_minutes": 720}
  ],
  "total_scheduled_minutes": 60,
  "total_available_minutes": 780
}
```

**list_labels** returns:
```json
[{"id": "456", "name": "school", "color": "blue", "order": 1, "is_favorite": false}]
```

**create_task / get_task / update_task** return the task summary object (same shape as list_tasks items).

**complete_task** returns:
```json
{"success": true, "task_id": "1234567890"}
```

**delete_task** returns:
```json
{"success": true, "task_id": "1234567890", "deleted": true}
```
