# GraphQL API

The platform exposes a **single GraphQL API** over HTTP. Every frontend, Learner app, Admin console, Auth service, talks exclusively to this API. Partner integrations use the same endpoint.

## Endpoint & transport

| Property | Value |
|---|---|
| Endpoint | Single GraphQL endpoint per deployment |
| Method | `POST` over HTTPS |
| Content-Type | `application/json` |
| Auth | JWT bearer token in the `Authorization` header |

## Authentication

Obtain a token via the login mutation, then send it on every request:

```http
POST /graphql HTTP/1.1
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Tokens are short-lived; refresh them through the auth domain rather than re-logging in.

## Main domains

| Domain | Covers |
|---|---|
| Auth / login | Login, token refresh, password reset, session management |
| Users | Profile, roles, preferences (locale, theme) |
| Courses / catalog | Course listing, catalog browsing, course detail |
| Enrollment | Enroll, unenroll, enrollment status |
| Progress | Unit/course completion, analytics data |
| Chat | Message history and sending (realtime delivery via Pusher) |
| Admin operations | User/group/course management, licenses, impersonation, settings |

## Example

**Query**

```graphql
query CourseProgress($courseId: ID!) {
  course(id: $courseId) {
    id
    title
    progress {
      completedUnits
      totalUnits
      percentComplete
    }
  }
}
```

**Variables**

```json
{
  "courseId": "course-v1:univo+demo+2026"
}
```

**Response**

```json
{
  "data": {
    "course": {
      "id": "course-v1:univo+demo+2026",
      "title": "Entrepreneurship Fundamentals",
      "progress": {
        "completedUnits": 14,
        "totalUnits": 20,
        "percentComplete": 70
      }
    }
  }
}
```

## Error conventions

- GraphQL errors are returned in the standard `errors` array, alongside partial `data` when available
- Authentication failures return `401 Unauthorized` at the HTTP level, refresh your token and retry
- Validation and business-logic errors include a machine-readable `extensions.code` field
- Rate limiting, when triggered, returns `429 Too Many Requests` with a `Retry-After` header

## Schema introspection

Schema introspection is available to **partners on request**. Contact your platform representative to have it enabled for your integration environment.
