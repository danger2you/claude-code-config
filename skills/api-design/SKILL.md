---
name: api-design
description: REST API design patterns including resource naming, status codes, pagination, filtering, error responses, versioning, and rate limiting for production APIs.
origin: ECC
---

# API Design Patterns

Conventions and best practices for designing consistent, developer-friendly REST APIs.

## When to Activate

- Designing new API endpoints
- Reviewing existing API contracts
- Adding pagination, filtering, or sorting
- Implementing error handling for APIs
- Planning API versioning strategy
- Building public or partner-facing APIs

## Resource Design

### URL Structure

```
# Resources are nouns, plural, lowercase, kebab-case
GET    /api/v1/users
GET    /api/v1/users/:id
POST   /api/v1/users
PUT    /api/v1/users/:id
PATCH  /api/v1/users/:id
DELETE /api/v1/users/:id

# Sub-resources for relationships
GET    /api/v1/users/:id/orders
POST   /api/v1/users/:id/orders

# Actions that don't map to CRUD (use verbs sparingly)
POST   /api/v1/orders/:id/cancel
POST   /api/v1/auth/login
POST   /api/v1/auth/refresh
```

### Naming Rules

```
# GOOD
/api/v1/team-members          # kebab-case for multi-word resources
/api/v1/orders?status=active  # query params for filtering
/api/v1/users/123/orders      # nested resources for ownership

# BAD
/api/v1/getUsers              # verb in URL
/api/v1/user                  # singular (use plural)
/api/v1/team_members          # snake_case in URLs
/api/v1/users/123/getOrders   # verb in nested resource
```

## HTTP Methods and Status Codes

### Method Semantics

| Method | Idempotent | Safe | Use For |
|--------|-----------|------|---------|
| GET | Yes | Yes | Retrieve resources |
| POST | No | No | Create resources, trigger actions |
| PUT | Yes | No | Full replacement of a resource |
| PATCH | No* | No | Partial update of a resource |
| DELETE | Yes | No | Remove a resource |

*PATCH can be made idempotent with proper implementation

### Status Code Reference

```
# Success
200 OK                    — GET, PUT, PATCH (with response body)
201 Created               — POST (include Location header)
204 No Content            — DELETE, PUT (no response body)

# Client Errors
400 Bad Request           — Validation failure, malformed JSON
401 Unauthorized          — Missing or invalid authentication
403 Forbidden             — Authenticated but not authorized
404 Not Found             — Resource doesn't exist
409 Conflict              — Duplicate entry, state conflict
422 Unprocessable Entity  — Semantically invalid (valid JSON, bad data)
429 Too Many Requests     — Rate limit exceeded

# Server Errors
500 Internal Server Error — Unexpected failure (never expose details)
502 Bad Gateway           — Upstream service failed
503 Service Unavailable   — Temporary overload, include Retry-After
```

### Common Mistakes

```
# BAD: 200 for everything
{ "status": 200, "success": false, "error": "Not found" }

# GOOD: Use HTTP status codes semantically
HTTP/1.1 404 Not Found
{ "error": { "code": "not_found", "message": "User not found" } }

# BAD: 500 for validation errors
# GOOD: 400 or 422 with field-level details

# BAD: 200 for created resources
# GOOD: 201 with Location header
HTTP/1.1 201 Created
Location: /api/v1/users/abc-123
```

## Response Format

### Unified Response Structure

All API responses use a unified `code`, `msg`, `data` structure:

```typescript
// Base response interface
interface BaseResponse<T = any> {
  code: number;           // Business status code
  msg: string;            // Message
  data: T;                // Response data
  timestamp?: string;     // Timestamp (ISO 8601)
  traceId?: string;       // Request trace ID
}

// Error response interface
interface ErrorResponse extends BaseResponse<null> {
  error?: {
    type: string;         // Error type
    detail: string;       // Detailed description
    field?: string;       // Error field
    value?: any;          // Error value
    fields?: Array<{      // Multi-field validation errors
      field: string;
      message: string;
      value?: any;
    }>;
  };
}

// Paginated response interface
interface PageResponse<T> extends BaseResponse<{
  records: T[];           // Data list
  pagination: {
    page: number;         // Current page
    size: number;         // Page size
    total: number;        // Total records
  };
}> {}

// List response interface (without pagination)
interface ListResponse<T> extends BaseResponse<T[]> {
  count?: number;         // List count
}
```

### Single Resource Response

**Success:**

```json
{
  "code": 200,
  "msg": "数据获取成功",
  "data": {
    "id": "abc-123",
    "email": "alice@example.com",
    "name": "Alice",
    "createdAt": "2025-01-15T10:30:00Z"
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Failure:**

```json
{
  "code": 404,
  "msg": "资源不存在",
  "data": null,
  "error": {
    "type": "RESOURCE_NOT_FOUND",
    "detail": "ID为abc-123的用户不存在",
    "field": "userId"
  },
  "timestamp": "2025-01-15T10:30:00Z",
  "traceId": "req-abc-123"
}
```

### List Response (Without Pagination)

**Success:**

```json
{
  "code": 200,
  "msg": "资源列表获取成功",
  "data": [
    { "id": "abc-123", "name": "Alice" },
    { "id": "def-456", "name": "Bob" }
  ],
  "count": 2,
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Failure:**

```json
{
  "code": 500,
  "msg": "资源列表获取失败",
  "data": [],
  "error": {
    "type": "DATABASE_ERROR",
    "detail": "数据库连接超时"
  },
  "timestamp": "2025-01-15T10:30:00Z",
  "traceId": "req-xyz-456"
}
```

### List Response (With Pagination)

**Success:**

```json
{
  "code": 200,
  "msg": "资源列表获取成功",
  "data": {
    "records": [
      { "id": "abc-123", "name": "Alice" },
      { "id": "def-456", "name": "Bob" }
    ],
    "pagination": {
      "page": 1,
      "size": 20,
      "total": 142
    }
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Failure:**

```json
{
  "code": 400,
  "msg": "分页参数错误",
  "data": null,
  "error": {
    "type": "INVALID_PARAMETER",
    "detail": "页码必须大于0",
    "field": "page",
    "value": -1
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

### Validation Error Response

```json
{
  "code": 422,
  "msg": "数据验证失败",
  "data": null,
  "error": {
    "type": "VALIDATION_ERROR",
    "detail": "请求数据不符合要求",
    "fields": [
      {
        "field": "email",
        "message": "邮箱格式不正确",
        "value": "invalid-email"
      },
      {
        "field": "age",
        "message": "年龄必须在18-100之间",
        "value": 150
      }
    ]
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

### Business Status Codes

```javascript
// Success codes
200: "操作成功"
201: "创建成功"
204: "删除成功"

// Client errors 4xx
400: "请求参数错误"
401: "未授权，请先登录"
403: "无权限访问"
404: "资源不存在"
409: "资源冲突"
422: "数据验证失败"
429: "请求过于频繁"

// Server errors 5xx
500: "服务器内部错误"
502: "网关错误"
503: "服务暂时不可用"
504: "网关超时"
```

## Pagination

### Offset-Based (Simple)

```
GET /api/v1/users?page=2&size=20

# Implementation
SELECT * FROM users
ORDER BY created_at DESC
LIMIT 20 OFFSET 20;
```

**Response Format:**

```json
{
  "code": 200,
  "msg": "用户列表获取成功",
  "data": {
    "records": [
      { "id": "abc-123", "name": "Alice" },
      { "id": "def-456", "name": "Bob" }
    ],
    "pagination": {
      "page": 2,
      "size": 20,
      "total": 142
    }
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Pros:** Easy to implement, supports "jump to page N"
**Cons:** Slow on large offsets (OFFSET 100000), inconsistent with concurrent inserts

### Cursor-Based (Scalable)

```
GET /api/v1/users?cursor=eyJpZCI6MTIzfQ&limit=20

# Implementation
SELECT * FROM users
WHERE id > :cursor_id
ORDER BY id ASC
LIMIT 21;  -- fetch one extra to determine has_next
```

**Response Format:**

```json
{
  "code": 200,
  "msg": "用户列表获取成功",
  "data": {
    "records": [...],
    "pagination": {
      "cursor": "eyJpZCI6MTQzfQ",
      "hasNext": true,
      "limit": 20
    }
  },
  "timestamp": "2025-01-15T10:30:00Z"
}
```

**Pros:** Consistent performance regardless of position, stable with concurrent inserts
**Cons:** Cannot jump to arbitrary page, cursor is opaque

### When to Use Which

| Use Case | Pagination Type |
|----------|----------------|
| Admin dashboards, small datasets (<10K) | Offset |
| Infinite scroll, feeds, large datasets | Cursor |
| Public APIs | Cursor (default) with offset (optional) |
| Search results | Offset (users expect page numbers) |

## Filtering, Sorting, and Search

### Filtering

```
# Simple equality
GET /api/v1/orders?status=active&customer_id=abc-123

# Comparison operators (use bracket notation)
GET /api/v1/products?price[gte]=10&price[lte]=100
GET /api/v1/orders?created_at[after]=2025-01-01

# Multiple values (comma-separated)
GET /api/v1/products?category=electronics,clothing

# Nested fields (dot notation)
GET /api/v1/orders?customer.country=US
```

### Sorting

```
# Single field (prefix - for descending)
GET /api/v1/products?sort=-created_at

# Multiple fields (comma-separated)
GET /api/v1/products?sort=-featured,price,-created_at
```

### Full-Text Search

```
# Search query parameter
GET /api/v1/products?q=wireless+headphones

# Field-specific search
GET /api/v1/users?email=alice
```

### Sparse Fieldsets

```
# Return only specified fields (reduces payload)
GET /api/v1/users?fields=id,name,email
GET /api/v1/orders?fields=id,total,status&include=customer.name
```

## Authentication and Authorization

### Token-Based Auth

```
# Bearer token in Authorization header
GET /api/v1/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

# API key (for server-to-server)
GET /api/v1/data
X-API-Key: sk_live_abc123
```

### Authorization Patterns

```typescript
// Resource-level: check ownership
app.get("/api/v1/orders/:id", async (req, res) => {
  const order = await Order.findById(req.params.id);
  if (!order) return res.status(404).json({ code: 404, msg: "资源不存在", data: null });
  if (order.userId !== req.user.id) return res.status(403).json({ code: 403, msg: "无权限访问", data: null });
  return res.json({ code: 200, msg: "数据获取成功", data: order });
});

// Role-based: check permissions
app.delete("/api/v1/users/:id", requireRole("admin"), async (req, res) => {
  await User.delete(req.params.id);
  return res.status(204).json({ code: 204, msg: "删除成功", data: null });
});
```

## Rate Limiting

### Headers

```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640000000
```

### Rate Limit Tiers

| Tier | Limit | Window | Use Case |
|------|-------|--------|----------|
| Anonymous | 30/min | Per IP | Public endpoints |
| Authenticated | 100/min | Per user | Standard API access |
| Premium | 1000/min | Per API key | Paid API plans |
| Internal | 10000/min | Per service | Service-to-service |

## Versioning

### URL Path Versioning (Recommended)

```
/api/v1/users
/api/v2/users
```

**Pros:** Explicit, easy to route, cacheable
**Cons:** URL changes between versions

### Header Versioning

```
GET /api/users
Accept: application/vnd.myapp.v2+json
```

**Pros:** Clean URLs
**Cons:** Harder to test, easy to forget

### Versioning Strategy

```
1. Start with /api/v1/ — don't version until you need to
2. Maintain at most 2 active versions (current + previous)
3. Deprecation timeline:
   - Announce deprecation (6 months notice for public APIs)
   - Add Sunset header: Sunset: Sat, 01 Jan 2026 00:00:00 GMT
   - Return 410 Gone after sunset date
4. Non-breaking changes don't need a new version:
   - Adding new fields to responses
   - Adding new optional query parameters
   - Adding new endpoints
5. Breaking changes require a new version:
   - Removing or renaming fields
   - Changing field types
   - Changing URL structure
   - Changing authentication method
```

## Implementation Patterns

### TypeScript Implementation Example

```typescript
// POST - 创建资源
export async function POST(req: NextRequest) {
  const parsed = schema.safeParse(await req.json());
  if (!parsed.success) {
    return NextResponse.json({
      code: 422, msg: "数据验证失败", data: null,
      error: { type: "VALIDATION_ERROR", fields: [...] }
    }, { status: 422 });
  }

  const user = await createUser(parsed.data);
  return NextResponse.json(
    { code: 201, msg: "创建成功", data: user },
    { status: 201, headers: { Location: `/api/v1/users/${user.id}` } }
  );
}

// GET - 单个资源
export async function GET(req: NextRequest, { params }) {
  const user = await findUserById(params.id);
  if (!user) {
    return NextResponse.json(
      { code: 404, msg: "资源不存在", data: null },
      { status: 404 }
    );
  }
  return NextResponse.json({ code: 200, msg: "数据获取成功", data: user });
}

// GET - 列表（带分页）
export async function GET(req: NextRequest) {
  const page = parseInt(req.nextUrl.searchParams.get("page") || "1");
  const size = parseInt(req.nextUrl.searchParams.get("size") || "20");
  const { records, total } = await findUsers({ page, size });

  return NextResponse.json({
    code: 200,
    msg: "资源列表获取成功",
    data: { records, pagination: { page, size, total } }
  });
}
```

## Special Case Response Examples

### Batch Operation Response

```json
{
  "code": 200,
  "msg": "批量操作完成",
  "data": {
    "success": [{ "id": 1212, "status": "已删除" }],
    "failed": [{ "id": 1213, "reason": "资源不存在" }],
    "summary": { "total": 2, "successCount": 1, "failedCount": 1 }
  }
}
```

### Rate Limit Response

```json
{
  "code": 429,
  "msg": "请求过于频繁，请稍后再试",
  "data": null,
  "error": {
    "type": "RATE_LIMIT_EXCEEDED",
    "retryAfter": 60,
    "limit": 100,
    "remaining": 0
  }
}
```

## API Design Checklist

Before shipping a new endpoint:

- [ ] Resource URL follows naming conventions (plural, kebab-case, no verbs)
- [ ] Correct HTTP method used (GET for reads, POST for creates, etc.)
- [ ] Appropriate status codes returned (not 200 for everything)
- [ ] Input validated with schema (Zod, Pydantic, Bean Validation)
- [ ] Error responses follow standard format with codes and messages
- [ ] Pagination implemented for list endpoints (cursor or offset)
- [ ] Authentication required (or explicitly marked as public)
- [ ] Authorization checked (user can only access their own resources)
- [ ] Rate limiting configured
- [ ] Response does not leak internal details (stack traces, SQL errors)
- [ ] Consistent naming with existing endpoints (camelCase vs snake_case)
- [ ] Documented (OpenAPI/Swagger spec updated)
