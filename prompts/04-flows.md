# Phase 4: Data Flow Analysis (Source-to-Sink)

You are conducting the data flow analysis phase of a comprehensive security code review. Your goal is to trace how untrusted data flows from entry points (sources) to sensitive operations (sinks), identify validation/sanitization checkpoints, and prioritize flows that intersect with high-value assets for deep vulnerability analysis.

## Inputs
- **Business Context**: `security-review/01-context.md`
- **Asset Inventory**: `security-review/02-assets.md`
- **Attack Surface**: `security-review/03-attack-surface.md`
- **Target Path**: `{TARGET_PATH}`
- **Selected Model**: `{MODEL}` (for complex flow tracing if needed)

## Objectives

1. **Identify Data Sources (Untrusted Input)**
   - User input (HTTP requests, WebSocket messages, CLI args)
   - File uploads
   - External API responses
   - Environment variables (in some contexts)
   - Database reads (if user-controlled content)

2. **Identify Data Sinks (Sensitive Operations)**
   - Database queries (SQL, NoSQL)
   - File system operations (read, write, delete)
   - Command execution (shell commands, system calls)
   - External API calls
   - Template rendering (HTML, email templates)
   - Response output (potential XSS)
   - Authentication/authorization decisions
   - Cryptographic operations

3. **Trace Data Flows from Source to Sink**
   - Follow data through function calls
   - Track transformations and assignments
   - Identify validation checkpoints
   - Map sanitization functions
   - Note where trust boundaries are crossed

4. **Assess Flow Security**
   - Is input validated before use?
   - Is output encoded for context?
   - Are dangerous sinks properly protected?
   - Can attacker control the data reaching the sink?

5. **Prioritize Flows by Risk**
   - Flows touching high-value assets (from Phase 2)
   - Flows through high-risk entry points (from Phase 3)
   - Flows with weak/missing validation
   - Flows to dangerous sinks (command execution, SQL)

## Analysis Process

### Step 1: Review Previous Phases
- Read all previous phase outputs to understand context, assets, and entry points
- Identify high-priority entry points from Phase 3

### Step 2: Map Common Sources
For each prioritized entry point from Phase 3:
- Identify what untrusted data enters (params, body, headers, files)
- Note the entry point location (`filepath:line`)
- List the data fields/variables

### Step 3: Identify Sinks in the Codebase
Search for dangerous operations:
- **SQL Queries**:
  - Raw SQL: `execute()`, `query()`, `exec()`, string concatenation in queries
  - ORM: Dynamic queries, `raw()`, `fragment()`, unsafe string interpolation
- **Command Execution**:
  - `exec()`, `system()`, `spawn()`, `shell()`, backticks, `Runtime.exec()`
- **File Operations**:
  - `readFile()`, `writeFile()`, `unlink()`, `open()`, path construction
- **Template Rendering**:
  - `render()`, `compile()`, `eval()`, innerHTML, dangerouslySetInnerHTML
- **External Calls**:
  - HTTP client calls with user-controlled URLs or data
- **Authentication/Authorization**:
  - Permission checks, role comparisons, session lookups using user input

### Step 4: Trace High-Priority Flows
For each prioritized entry point:
1. **Start at the source**: Entry point function/handler
2. **Follow the data**: Trace variables through the code
   - Function calls
   - Variable assignments
   - Object property access
   - Array operations
3. **Note transformations**:
   - Validation functions called
   - Sanitization applied
   - Type conversions
   - Encodings
4. **Reach the sink**: Document where data ends up
5. **Assess security**: Is there sufficient validation/sanitization?

Use grep, read, and code tracing to follow the flow. For complex flows, you may invoke the selected AI model for assistance.

### Step 5: Document Validation & Sanitization
For each flow, identify:
- **Input validation**: Type checks, format validation, whitelist/blacklist
- **Sanitization**: HTML encoding, SQL escaping, shell escaping
- **Context-appropriate encoding**: Output encoding for HTML, SQL, shell, etc.
- **Trust boundaries**: Where untrusted data becomes trusted (after validation)

### Step 6: Identify Asset Intersections
Cross-reference flows with assets from Phase 2:
- Which flows can access sensitive data?
- Which flows can modify high-value assets?
- Which flows involve authentication/authorization assets?
- Which flows handle financial or PII data?

### Step 7: Calculate Flow Risk Scores
For each flow, assess:
- **Source Risk** (1-5): How attacker-controlled is the source?
- **Sink Danger** (1-5): How dangerous is the sink operation?
- **Validation Strength** (1-5): How robust is validation? (5=strong, 1=none)
- **Asset Value** (1-5): How valuable is the asset involved? (from Phase 2)

**Flow Risk Score** = (Source Risk + Sink Danger + Asset Value) / Validation Strength

Higher scores = higher priority for Phase 5 vulnerability analysis.

### Step 8: Prioritize Top Flows
Select top 10-15 highest-risk flows for deep vulnerability analysis in Phase 5.

## Output Format

Create a markdown file with the following structure:

```markdown
# Security Review - Data Flow Analysis

**Application**: [Name]
**Phase**: Data Flow Analysis (Source-to-Sink)
**Date**: [Date]
**Inputs**: security-review/01-context.md, 02-assets.md, 03-attack-surface.md
**Model Used**: [Model name if used for complex tracing]

---

## Executive Summary

[2-3 paragraphs describing:
- Total data flows analyzed
- Flows with weak/no validation
- Flows intersecting high-value assets
- Key security concerns]

**Metrics**:
- Total Flows Analyzed: [count]
- Flows with Validation: [count]
- Flows without Validation: [count]
- High-Risk Flows (for Phase 5): [count]
- Flows touching PII/Sensitive Data: [count]

---

## Source-to-Sink Flow Inventory

### Flow Priority Matrix

[Visual representation of flow prioritization]

| Flow ID | Source (Entry Point) | Sink Type | Asset(s) Involved | Validation | Risk Score | Priority |
|---------|----------------------|-----------|-------------------|------------|------------|----------|
| FLOW-001 | POST /api/search | SQL Query | Product data | None | 8.5 | Critical |
| FLOW-002 | POST /admin/upload | File Write | Server filesystem | Weak | 7.8 | High |
| FLOW-003 | GET /api/user/:id | Database Read | User PII | Strong | 3.2 | Low |
| ... | ... | ... | ... | ... | ... | ... |

---

## Detailed Flow Analysis

### Critical Risk Flows (For Phase 5 Deep Dive)

#### FLOW-001: Search Query to SQL Database

**Overview**:
- **Source**: POST `/api/search` - Query parameter `q`
- **Sink**: Raw SQL query execution
- **Asset(s)**: Product database (may contain pricing, inventory)
- **Risk Score**: 8.5 (Critical)

**Data Flow Path**:
```
[Request] POST /api/search?q=<user_input>
    ↓
[routes/search.js:23] Extract query.q
    ↓
[controllers/search.js:45] searchProducts(userQuery)
    ↓
[models/product.js:67] db.query(`SELECT * FROM products WHERE name LIKE '%${userQuery}%'`)
    ↓
[Sink] SQL Execution (VULNERABLE - string concatenation)
```

**Code References**:
- Entry point: `routes/search.js:23`
- Controller: `controllers/search.js:45`
- Sink: `models/product.js:67`

**Validation Analysis**:
- ❌ No input validation on `q` parameter
- ❌ No SQL sanitization or parameterization
- ❌ Direct string interpolation into SQL query

**Potential Vulnerabilities**:
- SQL Injection (CWE-89)

**Asset Impact**:
- Can access/modify entire product database
- Potential lateral movement to other tables via UNION attacks
- Database credential exposure via information_schema

**Recommendation for Phase 5**:
- Develop SQL injection PoC
- Assess actual exploitability
- Determine data exfiltration potential

---

#### FLOW-002: Admin File Upload to Filesystem

**Overview**:
- **Source**: POST `/admin/upload` - File upload `document`
- **Sink**: File write to server filesystem
- **Asset(s)**: Server filesystem, potential code execution
- **Risk Score**: 7.8 (High)

**Data Flow Path**:
```
[Request] POST /admin/upload (multipart/form-data)
    ↓
[routes/admin.js:134] Extract file from req.files.document
    ↓
[middleware/upload.js:23] Multer processes upload
    ↓
[controllers/admin.js:78] saveFile(file.name, file.data, '/uploads/')
    ↓
[utils/files.js:45] fs.writeFile(`/uploads/${filename}`, data)
    ↓
[Sink] File write (VULNERABLE - path traversal possible)
```

**Code References**:
- Entry point: `routes/admin.js:134`
- Upload middleware: `middleware/upload.js:23`
- Sink: `utils/files.js:45`

**Validation Analysis**:
- ✅ Requires admin authentication
- ⚠️  Filename sanitization: Basic (replaces spaces only)
- ❌ No path traversal protection
- ❌ No file type validation (accepts all extensions)
- ❌ No magic byte verification

**Potential Vulnerabilities**:
- Path Traversal (CWE-22)
- Unrestricted File Upload (CWE-434)
- Potential Remote Code Execution if uploads/ in web root

**Asset Impact**:
- Can overwrite arbitrary files on server
- Potential to upload webshell
- Could modify application code if permissions allow

**Recommendation for Phase 5**:
- Test path traversal with `../../` sequences
- Attempt upload of executable files (.php, .jsp, .exe)
- Determine if uploads/ is in web-accessible path

---

#### FLOW-003: User Profile Update to Database

**Overview**:
- **Source**: PUT `/api/profile` - Body fields `name`, `email`, `bio`
- **Sink**: Database UPDATE query (parameterized)
- **Asset(s)**: User PII
- **Risk Score**: 3.2 (Low)

**Data Flow Path**:
```
[Request] PUT /api/profile
    ↓
[middleware/auth.js:12] Verify JWT, extract userId
    ↓
[routes/user.js:56] Extract body.name, body.email, body.bio
    ↓
[validators/user.js:34] Validate email format, name length, bio length
    ↓
[models/user.js:89] User.update(userId, {name, email, bio})
    ↓
[ORM] db.query("UPDATE users SET name=$1, email=$2, bio=$3 WHERE id=$4", [name, email, bio, userId])
    ↓
[Sink] Parameterized SQL (SAFE)
```

**Code References**:
- Entry point: `routes/user.js:56`
- Validation: `validators/user.js:34`
- Sink: `models/user.js:89`

**Validation Analysis**:
- ✅ JWT authentication required
- ✅ Input validation (format, length checks)
- ✅ Parameterized query (SQL injection protected)
- ✅ User can only update their own profile (userId from JWT)

**Potential Vulnerabilities**:
- None identified (well-protected flow)

**Asset Impact**:
- Limited to user's own PII
- Proper authorization prevents cross-user modification

**Recommendation for Phase 5**:
- Low priority - appears secure
- Could verify JWT validation strength
- Check for email uniqueness constraint bypass

---

[Repeat for all high/medium priority flows]

---

### High Risk Flows Summary

[Flows requiring deep analysis in Phase 5]

1. **FLOW-001**: Search → SQL Query (SQL Injection risk)
2. **FLOW-002**: Admin Upload → File Write (Path Traversal, RCE risk)
3. **FLOW-004**: Comment → Template Render (XSS risk)
4. **FLOW-007**: Webhook → Command Exec (Command Injection risk)
5. **FLOW-009**: Import CSV → Database (Mass Assignment, CSV Injection)
6. ...

Total: [X] critical/high-risk flows for Phase 5 analysis

---

### Medium Risk Flows Summary

[Flows with some validation but potential issues]

1. **FLOW-003**: Profile Update → Database (Low risk, well validated)
2. **FLOW-005**: Product Filter → Database (Parameterized but complex logic)
...

---

### Low Risk Flows Summary

[Well-protected flows, low priority for Phase 5]

1. **FLOW-012**: Static Content → CDN (No user data)
2. **FLOW-015**: Health Check → Metrics (Read-only, no sensitive data)
...

---

## Validation & Sanitization Analysis

### Validation Mechanisms Found

| Validation Function/Library | Location | Purpose | Strength |
|------------------------------|----------|---------|----------|
| `express-validator` | `validators/*.js` | Input validation | Strong (if used consistently) |
| `sanitize-html` | `utils/sanitize.js:12` | HTML sanitization | Strong |
| Custom `isEmail()` | `utils/validators.js:45` | Email format check | Medium (regex-based) |
| ... | ... | ... | ... |

### Sanitization Mechanisms Found

| Sanitization Function | Location | Context | Adequacy |
|-----------------------|----------|---------|----------|
| `db.escape()` | `models/legacy.js:*` | SQL escaping | Adequate for legacy code |
| Parameterized queries | `models/*.js` | SQL injection prevention | Strong |
| `escapeShellArg()` | `utils/shell.js:23` | Command injection prevention | Adequate |
| `he.encode()` | `views/helpers.js:12` | HTML entity encoding | Strong |
| ... | ... | ... | ... |

### Validation Gaps

[Where validation is missing or insufficient]

- **Missing Input Validation**:
  - `POST /api/search` - No validation on search query
  - `POST /admin/config` - Accepts arbitrary JSON without schema validation
  - `GET /api/export` - No validation on `format` parameter

- **Weak Sanitization**:
  - File upload filename sanitization insufficient (only replaces spaces)
  - CSV import doesn't escape formulas (CSV injection risk)

- **Context Mismatches**:
  - SQL escaping used where parameterization should be (legacy code in `models/legacy.js`)
  - HTML encoding missing in some template contexts

---

## Asset-Flow Intersection Analysis

### Flows Accessing High-Value Assets

[Cross-reference with Phase 2 assets]

| Asset (from Phase 2) | Flows That Access It | Risk Level |
|----------------------|----------------------|------------|
| User PII (emails, addresses) | FLOW-003, FLOW-011, FLOW-016 | Medium (mostly protected) |
| Payment Data | FLOW-008, FLOW-013 | High (external processor, good) |
| Admin Credentials | FLOW-006 (login) | Critical (needs review) |
| Database Credentials | None (hardcoded in env) | Critical (config issue, not flow) |
| AWS API Keys | FLOW-020 (S3 upload) | High (needs review) |
| ... | ... | ... |

### Unprotected Asset Access

[Assets accessible without proper validation]

- **User PII**: Accessible via FLOW-011 (search) without rate limiting → potential enumeration
- **Product Pricing**: Accessible via FLOW-001 (search) with SQL injection → data breach
- **Admin Functions**: FLOW-002 (upload) has weak path validation → privilege escalation

---

## Trust Boundary Analysis

### Identified Trust Boundaries

1. **Client → Server**
   - Boundary: HTTP request entry points
   - Trust Decision: JWT validation in `middleware/auth.js`
   - Strength: ✅ Strong (if JWT secret is strong)

2. **User → Admin**
   - Boundary: Admin-only endpoints
   - Trust Decision: Role check in `middleware/admin.js`
   - Strength: ⚠️  Medium (needs code review)

3. **Application → Database**
   - Boundary: Database queries
   - Trust Decision: Parameterized queries (mostly)
   - Strength: ✅ Strong where used, ❌ Weak in legacy code

4. **Application → External Services**
   - Boundary: API calls to Stripe, AWS, etc.
   - Trust Decision: API key authentication
   - Strength: ✅ Strong (keys in env vars)

### Trust Boundary Violations

[Where untrusted data crosses boundaries without validation]

- **FLOW-001**: User input → SQL query without validation (trust boundary violation)
- **FLOW-004**: User input → HTML template without encoding (trust boundary violation)
- **FLOW-007**: Webhook data → shell command without sanitization (trust boundary violation)

---

## Prioritization for Phase 5: Vulnerability Analysis

### Top Priority Flows (Must Analyze)

| Rank | Flow ID | Entry Point | Sink | Reason |
|------|---------|-------------|------|--------|
| 1 | FLOW-007 | Webhook Handler | Command Exec | Command injection, no validation, internet-facing |
| 2 | FLOW-001 | Search API | SQL Query | SQL injection, no validation, public endpoint |
| 3 | FLOW-002 | Admin Upload | File Write | Path traversal + RCE, admin-only but high impact |
| 4 | FLOW-004 | Comment Submit | Template Render | Stored XSS, affects all users viewing comments |
| 5 | FLOW-006 | Admin Login | Auth Decision | Potential auth bypass, critical asset |
| ... | ... | ... | ... | ... |

### Flows to Analyze (Time Permitting)

| Flow ID | Entry Point | Sink | Reason |
|---------|-------------|------|--------|
| FLOW-009 | CSV Import | Database | CSV injection + mass assignment |
| FLOW-011 | User Search | Database Read | User enumeration, timing attacks |
| ... | ... | ... | ... |

### Flows Deferred (Low Priority)

| Flow ID | Entry Point | Sink | Reason |
|---------|-------------|------|--------|
| FLOW-003 | Profile Update | Database | Well validated, low risk |
| FLOW-012 | Static Files | CDN | No user data involved |
| ... | ... | ... | ... |

---

## Appendix

### Sink Search Patterns Used

- SQL: `grep -r "query\(|execute\(|exec\(" src/`
- Command: `grep -r "exec\(|spawn\(|system\(" src/`
- File: `grep -r "writeFile\(|readFile\(|unlink\(" src/`
- Template: `grep -r "render\(|compile\(|innerHTML" src/`

### Flow Tracing Methodology

- Manual code tracing for 70% of flows
- Model-assisted tracing for 30% (complex async flows)
- Cross-referenced with test cases where available

### Files Analyzed
- [List all files examined during flow analysis]
```

## Analysis Instructions

1. **Read all previous phases**: Context, assets, attack surface
2. **Prioritize strategically**: Focus on high-risk entry points from Phase 3
3. **Trace systematically**: For each flow:
   - Find the source (entry point)
   - Follow the data through function calls
   - Identify validation/sanitization points
   - Reach the sink (dangerous operation)
   - Assess security
4. **Be specific**: Include exact file paths and line numbers for every step
5. **Score objectively**: Use the risk scoring formula consistently
6. **Connect to assets**: Cross-reference flows with Phase 2 assets
7. **Limit scope**: Don't analyze every flow - prioritize top 20-30 by risk
8. **Use model assistance wisely**: For complex flows, use the selected AI model to help trace, but verify its findings

## Tools to Use

- `Read`: Read source files to trace data flow
- `Grep`: Find sinks (query execution, file operations, command execution)
- `Glob`: Find related files (controllers, models, utilities)
- **Model** (if needed): Use `{MODEL}` to help trace complex async flows or large call chains
- Do NOT over-rely on model - verify all findings manually

## Completion Criteria

The analysis is complete when:
- ✅ High-priority entry points from Phase 3 have flows traced
- ✅ Top 10-15 highest-risk flows are documented in detail
- ✅ Validation and sanitization mechanisms are catalogued
- ✅ Flows are cross-referenced with high-value assets from Phase 2
- ✅ Flows are scored and prioritized for Phase 5
- ✅ Trust boundary violations are identified
- ✅ Output file `security-review/04-data-flows.md` is created

## After Completion

Present a summary to the user:
- Total flows analyzed
- Number of critical/high-risk flows identified
- Top 5 flows for deep vulnerability analysis
- Key validation gaps
- Flows touching the most valuable assets

Ask user: "Does this prioritization look correct? Should I proceed to Phase 5: Vulnerability Analysis for these [X] high-risk flows?"
