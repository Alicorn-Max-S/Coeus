# Canvas LMS — curl API Reference

```bash
# List courses
curl -s -H "Authorization: Bearer $CANVAS_API_TOKEN" \
  "$CANVAS_BASE_URL/api/v1/courses?enrollment_state=active&per_page=10"

# List assignments for a course
curl -s -H "Authorization: Bearer $CANVAS_API_TOKEN" \
  "$CANVAS_BASE_URL/api/v1/courses/COURSE_ID/assignments?per_page=10&order_by=due_at"

# Get a single assignment
curl -s -H "Authorization: Bearer $CANVAS_API_TOKEN" \
  "$CANVAS_BASE_URL/api/v1/courses/COURSE_ID/assignments/ASSIGNMENT_ID"

# Submit an assignment (text entry)
curl -s -X POST -H "Authorization: Bearer $CANVAS_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"submission": {"submission_type": "online_text_entry", "body": "My answer"}}' \
  "$CANVAS_BASE_URL/api/v1/courses/COURSE_ID/assignments/ASSIGNMENT_ID/submissions"

# Submit an assignment (URL)
curl -s -X POST -H "Authorization: Bearer $CANVAS_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"submission": {"submission_type": "online_url", "url": "https://example.com"}}' \
  "$CANVAS_BASE_URL/api/v1/courses/COURSE_ID/assignments/ASSIGNMENT_ID/submissions"
```

Canvas uses `Link` headers for pagination. The Python script handles pagination automatically.
