# API Reference

Complete API documentation for the BugBounty KSP platform.

## 🌐 Base URL

```
Development: http://localhost:3000/api/v1
Production: https://api.bugbounty-ksp.com/api/v1
```

## 🔐 Authentication

All protected endpoints require authentication using JWT tokens.

### Get Authentication Token

**POST** `/auth/login`

```json
// Request
{
  "email": "user@example.com",
  "password": "securepassword"
}

// Response
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "role": "researcher"
    }
  }
}
```

### Using the Token

Include the token in the Authorization header:

```
Authorization: Bearer <your_token>
```

## 👤 User Management

### Register New User

**POST** `/auth/register`

```json
// Request
{
  "email": "newuser@example.com",
  "password": "securepassword",
  "name": "John Doe",
  "role": "researcher"
}

// Response (201 Created)
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "newuser@example.com",
    "name": "John Doe",
    "role": "researcher",
    "created_at": "2026-02-04T08:55:36.952Z"
  }
}
```

### Get Current User

**GET** `/users/me` 🔒

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "researcher",
    "profile": {
      "bio": "Security researcher",
      "skills": ["Web Security", "Cryptography"]
    }
  }
}
```

### Update User Profile

**PATCH** `/users/me` 🔒

```json
// Request
{
  "name": "Jane Doe",
  "profile": {
    "bio": "Senior Security Researcher",
    "skills": ["Web Security", "Cryptography", "Reverse Engineering"]
  }
}

// Response (200 OK)
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Jane Doe",
    "profile": {
      "bio": "Senior Security Researcher",
      "skills": ["Web Security", "Cryptography", "Reverse Engineering"]
    }
  }
}
```

## 🎯 Bounty Programs

### List All Programs

**GET** `/programs`

Query Parameters:
- `status` - Filter by status (active, paused, closed)
- `page` - Page number (default: 1)
- `limit` - Items per page (default: 20, max: 100)
- `sort` - Sort field (created_at, reward_max, name)
- `order` - Sort order (asc, desc)

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "programs": [
      {
        "id": "uuid",
        "name": "Web Application Security",
        "description": "Find vulnerabilities in our web app",
        "status": "active",
        "reward_range": {
          "min": 100,
          "max": 10000,
          "currency": "USD"
        },
        "scope": {
          "in_scope": ["*.example.com", "api.example.com"],
          "out_of_scope": ["test.example.com"]
        },
        "created_at": "2026-01-01T00:00:00.000Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 45,
      "pages": 3
    }
  }
}
```

### Get Program Details

**GET** `/programs/:id`

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Web Application Security",
    "description": "Find vulnerabilities in our web app",
    "status": "active",
    "reward_range": {
      "min": 100,
      "max": 10000,
      "currency": "USD"
    },
    "scope": {
      "in_scope": ["*.example.com"],
      "out_of_scope": ["test.example.com"]
    },
    "rules": "...",
    "statistics": {
      "total_reports": 125,
      "accepted_reports": 89,
      "total_paid": 45000
    }
  }
}
```

### Create Program

**POST** `/programs` 🔒 (Admin/Client only)

```json
// Request
{
  "name": "Mobile App Security",
  "description": "Security testing for our mobile applications",
  "status": "active",
  "reward_range": {
    "min": 200,
    "max": 15000,
    "currency": "USD"
  },
  "scope": {
    "in_scope": ["iOS App", "Android App"],
    "out_of_scope": []
  }
}

// Response (201 Created)
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Mobile App Security",
    "status": "active",
    "created_at": "2026-02-04T08:55:36.952Z"
  }
}
```

## 🐛 Vulnerability Reports

### Submit Report

**POST** `/reports` 🔒

```json
// Request
{
  "program_id": "uuid",
  "title": "SQL Injection in Login Form",
  "severity": "critical",
  "description": "Detailed vulnerability description...",
  "steps_to_reproduce": "1. Navigate to...\n2. Enter payload...",
  "impact": "Attackers can access all user data",
  "proof_of_concept": "Screenshot or video URL",
  "suggested_fix": "Use parameterized queries"
}

// Response (201 Created)
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "SQL Injection in Login Form",
    "status": "new",
    "severity": "critical",
    "submitted_at": "2026-02-04T08:55:36.952Z"
  }
}
```

### List Reports

**GET** `/reports` 🔒

Query Parameters:
- `program_id` - Filter by program
- `status` - Filter by status (new, triaged, accepted, rejected, resolved)
- `severity` - Filter by severity (critical, high, medium, low)
- `page`, `limit` - Pagination

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "reports": [
      {
        "id": "uuid",
        "title": "SQL Injection in Login Form",
        "severity": "critical",
        "status": "triaged",
        "program": {
          "id": "uuid",
          "name": "Web Application Security"
        },
        "researcher": {
          "id": "uuid",
          "name": "John Doe"
        },
        "submitted_at": "2026-02-04T08:55:36.952Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 89,
      "pages": 5
    }
  }
}
```

### Get Report Details

**GET** `/reports/:id` 🔒

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "SQL Injection in Login Form",
    "severity": "critical",
    "status": "accepted",
    "description": "Detailed description...",
    "steps_to_reproduce": "...",
    "impact": "...",
    "proof_of_concept": "...",
    "timeline": [
      {
        "event": "submitted",
        "timestamp": "2026-02-01T00:00:00.000Z"
      },
      {
        "event": "triaged",
        "timestamp": "2026-02-02T00:00:00.000Z"
      },
      {
        "event": "accepted",
        "timestamp": "2026-02-03T00:00:00.000Z",
        "reward": 5000
      }
    ]
  }
}
```

### Update Report Status

**PATCH** `/reports/:id/status` 🔒 (Admin/Client only)

```json
// Request
{
  "status": "accepted",
  "reward": 5000,
  "notes": "Excellent find! Critical vulnerability confirmed."
}

// Response (200 OK)
{
  "success": true,
  "data": {
    "id": "uuid",
    "status": "accepted",
    "reward": 5000
  }
}
```

## 📊 Statistics & Analytics

### Get Dashboard Statistics

**GET** `/analytics/dashboard` 🔒

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "total_programs": 12,
    "active_programs": 8,
    "total_reports": 456,
    "pending_reports": 23,
    "total_rewards_paid": 125000,
    "average_response_time": 2.5,
    "severity_breakdown": {
      "critical": 12,
      "high": 45,
      "medium": 89,
      "low": 67
    }
  }
}
```

## 🔔 Notifications

### Get Notifications

**GET** `/notifications` 🔒

```json
// Response (200 OK)
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "uuid",
        "type": "report_update",
        "title": "Report Status Updated",
        "message": "Your report has been accepted",
        "read": false,
        "created_at": "2026-02-04T08:55:36.952Z"
      }
    ],
    "unread_count": 3
  }
}
```

### Mark Notification as Read

**PATCH** `/notifications/:id/read` 🔒

```json
// Response (200 OK)
{
  "success": true
}
```

## ⚠️ Error Responses

All error responses follow this format:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### Common HTTP Status Codes

- `200 OK` - Request succeeded
- `201 Created` - Resource created successfully
- `400 Bad Request` - Invalid request data
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `422 Unprocessable Entity` - Validation error
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error

## 📝 Rate Limiting

API requests are rate-limited to prevent abuse:

- **Authenticated**: 1000 requests per hour
- **Unauthenticated**: 100 requests per hour

Rate limit headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1643961600
```

## 🔗 Webhooks

Configure webhooks to receive real-time notifications.

**POST** `/webhooks` 🔒 (Admin only)

```json
// Request
{
  "url": "https://your-server.com/webhook",
  "events": ["report.submitted", "report.accepted"],
  "secret": "your_webhook_secret"
}
```

### Webhook Events

- `report.submitted` - New report submitted
- `report.accepted` - Report accepted
- `report.rejected` - Report rejected
- `report.resolved` - Report resolved
- `program.created` - New program created

## 📚 Additional Resources

- [Postman Collection](#)
- [OpenAPI/Swagger Spec](#)
- [API Changelog](#)

---

[← Architecture](architecture.md) | [Home](index.md) | [Next: Frontend Components →](frontend-components.md)
