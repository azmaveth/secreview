# Phase 3: Attack Surface Enumeration

You are conducting the attack surface enumeration phase of a comprehensive security code review. Your goal is to systematically map every point where the outside world can interact with the application and identify trust boundaries.

## Inputs
- **Business Context**: `security-review/01-context.md`
- **Asset Inventory**: `security-review/02-assets.md`
- **Target Path**: `{TARGET_PATH}`

## Objectives

1. **Enumerate All Entry Points**
   - HTTP/HTTPS endpoints (REST APIs, web pages)
   - WebSocket connections
   - GraphQL endpoints
   - gRPC services
   - CLI command-line arguments
   - File inputs (uploads, imports, configuration files)
   - Database interfaces (if exposed)
   - Message queue consumers
   - Scheduled jobs that process external data

2. **Map Authentication Boundaries**
   - Public (unauthenticated) endpoints
   - Authenticated endpoints
   - Endpoints with different privilege requirements
   - Anonymous vs. identified users

3. **Identify Input Vectors**
   - Query parameters
   - Request bodies (JSON, XML, form data, multipart)
   - HTTP headers
   - Cookies
   - File uploads
   - WebSocket messages
   - Environment variables
   - Configuration files

4. **Document Trust Boundaries**
   - Client/Server boundary
   - Internet/Internal network boundary
   - User/Admin boundary
   - Service/Service boundaries (in microservices)
   - Process boundaries

5. **Assess Attack Surface Exposure**
   - Internet-facing vs. internal-only
   - Rate limiting presence
   - Input validation at entry points
   - Size/complexity of attack surface

## Analysis Process

### Step 1: Review Previous Phases
- Read `security-review/01-context.md` to understand the application type
- Read `security-review/02-assets.md` to know what assets to protect

### Step 2: Enumerate HTTP/API Endpoints
Find and analyze:
- **Express (Node.js)**: `app.get()`, `app.post()`, `router.get()`, etc.
- **Flask/Django (Python)**: Route decorators, URLconf files
- **Phoenix (Elixir)**: Router files (`router.ex`), controller actions
- **Rails (Ruby)**: `config/routes.rb`
- **Spring (Java)**: `@RequestMapping`, `@GetMapping`, etc.
- **FastAPI (Python)**: Route decorators with `@app.get()`, etc.
- **Go**: HTTP handler registrations

For each endpoint, document:
- Path pattern (e.g., `/api/users/:id`)
- HTTP method (GET, POST, PUT, DELETE, PATCH)
- Authentication requirement (public, user, admin)
- Input parameters (path, query, body, headers)
- Response type

### Step 3: Find WebSocket/Channel Endpoints
Search for:
- WebSocket server setup
- Phoenix Channel definitions
- Socket.io event handlers
- SignalR hubs
- GraphQL subscriptions

### Step 4: Identify GraphQL Schema (if applicable)
Analyze:
- GraphQL schema definitions
- Queries, mutations, subscriptions
- Authorization directives
- Input types

### Step 5: Map CLI Entry Points (if applicable)
Find:
- Command definitions
- Argument parsers
- Option/flag definitions
- Input validation

### Step 6: Locate File Input Handlers
Search for:
- File upload endpoints
- File import/export functionality
- Configuration file parsers
- CSV/Excel processors
- Image/document processors

### Step 7: Identify Background Job Entry Points
Find:
- Cron jobs that process external data
- Message queue consumers (RabbitMQ, Kafka, SQS)
- Webhook handlers
- Email processors

### Step 8: Analyze Authentication & Authorization
For each entry point, determine:
- Is authentication required?
- What authentication method? (session, JWT, API key, OAuth)
- What authorization checks are performed?
- Can anonymous users access?
- What roles/permissions are needed?

### Step 9: Map Trust Boundaries
Identify:
- Where untrusted data enters the system
- Where trust decisions are made
- Where privilege boundaries exist
- Where data crosses network boundaries

### Step 10: Assess Attack Surface Characteristics
Evaluate:
- Total number of entry points
- Ratio of public to authenticated endpoints
- Complexity of input validation
- Presence of security controls (rate limiting, input validation, WAF)

## Output Format

Create a markdown file with the following structure:

```markdown
# Security Review - Attack Surface Enumeration

**Application**: [Name]
**Phase**: Attack Surface Enumeration
**Date**: [Date]
**Inputs**: security-review/01-context.md, security-review/02-assets.md

---

## Executive Summary

[2-3 paragraph summary of the attack surface size, complexity, and key concerns]

**Attack Surface Metrics**:
- Total Entry Points: [count]
- Public (Unauthenticated): [count]
- Authenticated: [count]
- Admin/Privileged: [count]
- File Upload Endpoints: [count]
- WebSocket/Real-time: [count]

**Key Findings**:
- [Finding 1]
- [Finding 2]
- [Finding 3]

---

## Attack Surface Map

### Trust Boundaries

```
[Visual representation of trust boundaries]

Example:
┌──────────────────────────────────────────────────────┐
│                  Internet (Untrusted)                │
└─────────────────────┬────────────────────────────────┘
                      │
         ┌────────────▼────────────┐
         │   Web Application       │
         │   (Trust Boundary)      │
         │   - Auth Check          │
         │   - Input Validation    │
         └────────────┬────────────┘
                      │
      ┌───────────────┼───────────────┐
      │               │               │
┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼─────┐
│  Database │  │   Redis   │  │  AWS S3   │
│ (Trusted) │  │ (Trusted) │  │ (Trusted) │
└───────────┘  └───────────┘  └───────────┘
```

### Authentication Boundaries

| Boundary | Entry Points | Authentication Method | Authorization Model |
|----------|--------------|----------------------|---------------------|
| Public | [count] endpoints | None | N/A |
| User | [count] endpoints | [JWT / Session / etc.] | [RBAC / ABAC] |
| Admin | [count] endpoints | [Same + additional] | [Permission check] |
| Service | [count] endpoints | [API Key / mTLS] | [Service ACL] |

---

## Entry Point Inventory

### 1. HTTP/HTTPS Endpoints

#### 1.1 Public Endpoints (Unauthenticated)

| Method | Path | Purpose | Input Vectors | File Location | Notes |
|--------|------|---------|---------------|---------------|-------|
| GET | `/` | Home page | Query params | `routes/index.js:12` | Static content |
| POST | `/api/register` | User registration | Body: email, password | `routes/auth.js:45` | Creates user account |
| POST | `/api/login` | User login | Body: email, password | `routes/auth.js:67` | Returns JWT |
| GET | `/api/products` | List products | Query: page, limit, search | `routes/products.js:23` | Public catalog |
| ... | ... | ... | ... | ... | ... |

**Total Public Endpoints**: [count]

**Security Observations**:
- Rate limiting: [Present on X endpoints / Absent]
- Input validation: [Describe]
- CORS policy: [Describe or "Not configured"]

#### 1.2 Authenticated User Endpoints

| Method | Path | Purpose | Auth Required | Input Vectors | File Location | Notes |
|--------|------|---------|---------------|---------------|---------------|-------|
| GET | `/api/profile` | Get user profile | JWT | None | `routes/user.js:34` | Returns PII |
| PUT | `/api/profile` | Update profile | JWT | Body: name, email, phone | `routes/user.js:56` | Updates PII |
| POST | `/api/orders` | Create order | JWT | Body: items, address | `routes/orders.js:12` | Financial transaction |
| ... | ... | ... | ... | ... | ... | ... |

**Total Authenticated Endpoints**: [count]

**Security Observations**:
- Authorization checks: [Consistent / Inconsistent / Missing in X places]
- CSRF protection: [Present / Absent]

#### 1.3 Administrative Endpoints

| Method | Path | Purpose | Auth Required | Input Vectors | File Location | Notes |
|--------|------|---------|---------------|---------------|---------------|-------|
| GET | `/admin/users` | List all users | Admin JWT | Query: filters | `routes/admin.js:23` | Exposes all PII |
| DELETE | `/admin/users/:id` | Delete user | Admin JWT | Path param: id | `routes/admin.js:78` | Dangerous operation |
| POST | `/admin/config` | Update config | Admin JWT | Body: config JSON | `routes/admin.js:134` | System-wide changes |
| ... | ... | ... | ... | ... | ... | ... |

**Total Admin Endpoints**: [count]

**Security Observations**:
- Admin check implementation: `[filepath:line]`
- Audit logging: [Present / Absent]
- Privilege escalation risks: [Describe]

#### 1.4 Service/API Endpoints

[If applicable - service-to-service APIs]

| Method | Path | Purpose | Auth Required | Input Vectors | File Location |
|--------|------|---------|---------------|---------------|---------------|
| ... | ... | ... | ... | ... | ... |

---

### 2. WebSocket/Real-Time Endpoints

[If applicable]

| Channel/Event | Purpose | Auth Required | Input Message Format | File Location | Notes |
|---------------|---------|---------------|----------------------|---------------|-------|
| `/socket/chat` | Real-time chat | JWT in connection | JSON: {message, room} | `channels/chat.ex:12` | Broadcasts to room |
| `/ws/notifications` | Push notifications | Session cookie | N/A (receive only) | `ws/notifications.js:45` | One-way |
| ... | ... | ... | ... | ... | ... |

**Security Observations**:
- Connection authentication: [How verified]
- Message validation: [Describe]
- Rate limiting: [Present / Absent]

---

### 3. GraphQL Endpoints

[If applicable]

#### Schema Overview
- Endpoint: `[/graphql]`
- Introspection enabled: [Yes / No]
- Authentication: [How handled]

#### Queries
| Query | Purpose | Auth Required | Input Arguments | File Location |
|-------|---------|---------------|-----------------|---------------|
| `users` | List users | Yes | filters, pagination | `schema/user.js:23` |
| ... | ... | ... | ... | ... |

#### Mutations
| Mutation | Purpose | Auth Required | Input Arguments | File Location | Risk |
|----------|---------|---------------|-----------------|---------------|------|
| `createUser` | Create user | Admin | UserInput | `schema/user.js:67` | Medium |
| `deleteAccount` | Delete account | User (own) or Admin | userId | `schema/user.js:89` | High |
| ... | ... | ... | ... | ... | ... |

#### Subscriptions
[If applicable]

**Security Observations**:
- Query depth limiting: [Yes / No]
- Query cost analysis: [Yes / No]
- Authorization in resolvers: [Consistent / Inconsistent]

---

### 4. File Upload Entry Points

| Endpoint | Method | Purpose | Auth Required | File Types Accepted | Max Size | File Location | Storage |
|----------|--------|---------|---------------|---------------------|----------|---------------|---------|
| `/api/upload/avatar` | POST | Profile picture | User | image/* | 5MB | `routes/upload.js:23` | S3 |
| `/api/upload/document` | POST | Document upload | User | pdf, docx | 10MB | `routes/upload.js:56` | Local disk |
| ... | ... | ... | ... | ... | ... | ... | ... |

**Security Observations**:
- File type validation: [Whitelist / Blacklist / None]
- Content inspection: [Magic byte check / Extension only]
- Malware scanning: [Yes / No]
- Path traversal protection: [Describe]
- Filename sanitization: [Describe]

---

### 5. CLI Entry Points

[If applicable - for CLI tools]

| Command | Subcommand | Purpose | Input Sources | File Location | Auth/Permissions |
|---------|------------|---------|---------------|---------------|------------------|
| `app` | `import` | Import data | --file <path> | `cli/import.js:12` | Runs as user |
| ... | ... | ... | ... | ... | ... |

**Security Observations**:
- Input validation: [Describe]
- Command injection risks: [Describe]

---

### 6. Background Job Entry Points

[Jobs that process external data]

| Job Type | Trigger | Data Source | Processing Logic | File Location | Validation |
|----------|---------|-------------|------------------|---------------|------------|
| Webhook handler | HTTP POST | External service | Parse JSON, update DB | `jobs/webhook.js:34` | [Signature verification / None] |
| Email processor | IMAP poll | Email server | Parse email, extract attachments | `jobs/email.js:67` | [SPF check / None] |
| Queue consumer | SQS message | AWS SQS | Process message | `jobs/queue.js:23` | [Schema validation] |
| ... | ... | ... | ... | ... | ... |

**Security Observations**:
- Message authentication: [Describe]
- Replay protection: [Yes / No]
- Error handling: [Fails open / Fails closed]

---

### 7. Other Entry Points

[Any additional entry points not covered above]

- **Database Console**: [Exposed / Not exposed]
- **Metrics Endpoint**: [Public / Protected / None]
- **Health Check**: [Public / Protected] - `[endpoint]`
- **Debug Endpoints**: [Found / Not found]
- **Admin Panel**: [Location] - Auth: [Method]

---

## Input Vector Analysis

### Query Parameters
[List endpoints accepting query parameters and what they do with them]

| Endpoint | Parameters | Purpose | Validation | Risk |
|----------|------------|---------|------------|------|
| `/api/search` | `q`, `limit` | Search query | [Sanitization?] | [SQL injection risk?] |
| ... | ... | ... | ... | ... |

### Request Bodies
[Analyze complexity and validation of request bodies]

**JSON Inputs**: [count] endpoints
**Form Data**: [count] endpoints
**Multipart**: [count] endpoints (file uploads)
**XML**: [count] endpoints

**Validation Approach**: [JSON Schema / Manual checks / Framework validation / None]

### HTTP Headers
[Endpoints that read custom headers or make security decisions based on headers]

| Endpoint | Header | Purpose | Trustworthiness | Risk |
|----------|--------|---------|-----------------|------|
| `/api/*` | `Authorization` | Auth token | Verified via JWT | Low (if properly validated) |
| `/api/admin/*` | `X-Admin-Key` | Admin auth | [How verified?] | [High if weak] |
| ... | ... | ... | ... | ... |

### Cookies
[Cookie-based authentication or session management]

- **Session Cookie**: `[name]`
  - HttpOnly: [Yes / No]
  - Secure: [Yes / No]
  - SameSite: [Strict / Lax / None]
  - Domain: [domain]
  - Path: [path]

---

## Attack Surface Characteristics

### Surface Area Analysis

**Complexity Score**: [Low / Medium / High / Very High]
- Based on: [number of endpoints × input vectors × data transformations]

**Exposure Level**: [Internal / DMZ / Internet-facing]

**Rate Limiting Coverage**: [X% of endpoints protected]

**Input Validation Coverage**: [Comprehensive / Partial / Minimal / None]

### High-Risk Entry Points

[Flag entry points that are particularly risky due to complexity, privileges, or asset access]

1. **[Endpoint/Entry Point]**
   - **Risk Level**: [Critical / High / Medium]
   - **Reason**: [Why it's high risk - touches sensitive assets, complex logic, admin access, etc.]
   - **Asset Exposure**: [Which assets from Phase 2 can be reached via this entry point]
   - **Location**: `[filepath:line]`

2. **[Endpoint/Entry Point]**
   - [Same format]

3. ...

### Security Control Gaps

[Identify missing security controls across the attack surface]

- **Missing Rate Limiting**: [List endpoints without rate limiting]
- **Missing Authentication**: [Public endpoints that should be protected]
- **Missing Input Validation**: [Endpoints accepting unvalidated input]
- **Missing CSRF Protection**: [State-changing endpoints without CSRF tokens]
- **Missing Authorization**: [Endpoints not checking user permissions]

---

## Prioritization for Data Flow Analysis

Based on attack surface analysis and asset inventory, the following entry points should be prioritized in Phase 4 (Data Flow Analysis):

| Priority | Entry Point | Reason | Assets at Risk |
|----------|-------------|--------|----------------|
| 1 | `[endpoint]` | [Why - e.g., admin function, touches financial data, public + dangerous] | [Asset names from Phase 2] |
| 2 | `[endpoint]` | [Reason] | [Assets] |
| 3 | `[endpoint]` | [Reason] | [Assets] |
| ... | ... | ... | ... |

---

## Appendix

### Route Files Analyzed
- `[filepath]` - [Framework/type]
- `[filepath]` - [Framework/type]

### Search Patterns Used
- Searched for: `[pattern]` - Found: [count] matches
- Searched for: `[pattern]` - Found: [count] matches

### Endpoint Discovery Methods
- [Manual code review / Automated scanning / Route file parsing]
```

## Analysis Instructions

1. **Read previous phases**: Understand the app and its assets before mapping attack surface
2. **Be systematic**: Use framework-specific patterns to find ALL entry points
3. **Be thorough**: Don't just find routes - analyze auth, inputs, and risks for each
4. **Cross-reference assets**: Connect entry points to the assets they can access
5. **Assess controls**: Note presence/absence of security controls at each entry point
6. **Think attacker mindset**: Which entry points are most attractive to attackers?
7. **Prioritize**: Rank entry points by risk for data flow analysis

## Tools to Use

- `Read`: Read route files, controllers, channel definitions
- `Glob`: Find route files (`**/*route*.{js,py,rb,ex}`, `**/router.{js,ex}`)
- `Grep`: Search for HTTP method decorators, route definitions, endpoint patterns
- Do NOT use external AI tools - perform analysis directly

## Completion Criteria

The analysis is complete when:
- ✅ All HTTP/API endpoints are enumerated
- ✅ WebSocket/real-time endpoints are documented (if applicable)
- ✅ File upload points are identified
- ✅ Background job entry points are found
- ✅ Authentication/authorization is mapped for each entry point
- ✅ High-risk entry points are flagged
- ✅ Entry points are prioritized for data flow analysis
- ✅ Output file `security-review/03-attack-surface.md` is created

## After Completion

Present a summary to the user:
- Total attack surface size (number of entry points)
- Breakdown by authentication requirement (public/user/admin)
- Top 5 highest-risk entry points
- Key security control gaps

Ask user: "Does this attack surface map look complete? Should I proceed to Phase 4: Data Flow Analysis?"
