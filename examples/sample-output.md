# Security Code Review Skill - Sample Output

This document demonstrates what the output of the `secreview` skill looks like for a hypothetical Node.js/Express web application.

---

## Sample Invocation

```bash
/secreview ./my-web-app --model gemini-2.5-pro
```

---

## Phase 1: Business Context Discovery

**Output File**: `security-review/01-context.md`

**Summary Presented to User**:
```
✅ Phase 1: Business Context Discovery - Complete

Application: MyWebApp - E-commerce Platform
Purpose: Online retail platform for selling consumer electronics

Technology Stack:
- Language: Node.js v18.x
- Framework: Express v4.18
- Database: PostgreSQL 14
- Authentication: JWT with bcrypt password hashing

Entry Points Identified: 47 HTTP endpoints
- 12 public (unauthenticated)
- 28 authenticated user endpoints
- 7 admin-only endpoints

Key Security-Relevant Observations:
- Uses parameterized queries via pg library (positive)
- File upload functionality found (needs closer analysis)
- Custom authentication implementation (not using proven library)

Does this context analysis look accurate? Should I proceed to Phase 2: Asset Identification?
```

---

## Phase 2: Asset Identification

**Output File**: `security-review/02-assets.md`

**Summary Presented to User**:
```
✅ Phase 2: Asset Identification - Complete

Total High-Value Assets: 15

Crown Jewels (Top 3):
1. Customer PII Database (emails, addresses, phone numbers) - 50,000 records
2. Payment Processing API Keys (Stripe secret key) - Full transaction access
3. Admin Credentials - Can access all user data and system settings

At-Risk Assets: 4
- JWT signing secret (stored in .env but weak: 12 characters)
- File upload directory (publicly accessible, no type validation)
- User session tokens (no HttpOnly flag, XSS-vulnerable)
- Database connection string (includes credentials in plaintext)

Key Concerns:
- Payment data flows through application (PCI-DSS scope)
- Customer PII subject to GDPR
- Weak password policy (minimum 6 characters, no complexity requirements)

Would you like to add any additional assets to this inventory, or should I proceed to Phase 3: Attack Surface Enumeration?
```

---

## Phase 3: Attack Surface Enumeration

**Output File**: `security-review/03-attack-surface.md`

**Summary Presented to User**:
```
✅ Phase 3: Attack Surface Enumeration - Complete

Attack Surface Metrics:
- Total Entry Points: 47
- Public (Unauthenticated): 12
- Authenticated: 28
- Admin/Privileged: 7
- File Upload Endpoints: 2
- WebSocket/Real-time: 0

Top 5 Highest-Risk Entry Points:
1. POST /api/search - Public, accepts user query, no rate limiting
2. POST /admin/upload - Admin, file upload with weak validation
3. POST /api/register - Public, no CAPTCHA, mass account creation possible
4. GET /api/user/:id - Auth required but IDOR risk (no ownership check)
5. PUT /admin/config - Admin, accepts arbitrary JSON configuration

Security Control Gaps:
- 8 endpoints missing rate limiting (including login, registration, search)
- 5 endpoints missing input validation
- CSRF protection absent on all state-changing endpoints
- No WAF or API gateway protection

Does this attack surface map look complete? Should I proceed to Phase 4: Data Flow Analysis?
```

---

## Phase 4: Data Flow Analysis

**Output File**: `security-review/04-data-flows.md`

**Summary Presented to User**:
```
✅ Phase 4: Data Flow Analysis - Complete

Total Flows Analyzed: 23
- Flows with validation: 8
- Flows without validation: 15
- High-risk flows (for Phase 5): 10

Top 5 Flows for Deep Vulnerability Analysis:

1. FLOW-001: Search Query → SQL Database (Risk Score: 9.2)
   - Source: POST /api/search, query param 'q'
   - Sink: Raw SQL query execution in models/product.js:67
   - Validation: NONE
   - Asset: Product database (contains pricing, inventory)
   - Vulnerability: SQL Injection (CWE-89)

2. FLOW-002: Admin File Upload → Filesystem (Risk Score: 8.5)
   - Source: POST /admin/upload, multipart file
   - Sink: fs.writeFile() in utils/files.js:45
   - Validation: WEAK (filename sanitization only replaces spaces)
   - Asset: Server filesystem, potential code execution
   - Vulnerability: Path Traversal (CWE-22), Unrestricted Upload (CWE-434)

3. FLOW-003: User Comment → HTML Template (Risk Score: 8.0)
   - Source: POST /api/comments, body.comment
   - Sink: Template rendering in views/product.ejs:34
   - Validation: NONE (no HTML sanitization)
   - Asset: All users viewing product pages
   - Vulnerability: Stored XSS (CWE-79)

4. FLOW-004: Webhook Handler → Command Execution (Risk Score: 9.8)
   - Source: POST /webhooks/stripe, body.event
   - Sink: exec() in webhooks/stripe.js:89
   - Validation: Signature check but command not sanitized
   - Asset: Server OS, potential full compromise
   - Vulnerability: Command Injection (CWE-78)

5. FLOW-005: CSV Import → Database (Risk Score: 7.5)
   - Source: POST /admin/import, file upload
   - Sink: Bulk INSERT in models/import.js:123
   - Validation: CSV parsing but no formula escaping
   - Asset: Product/user database
   - Vulnerability: CSV Injection (CWE-1236), Mass Assignment (CWE-915)

Flows Touching Most Valuable Assets:
- Customer PII: FLOW-007, FLOW-011, FLOW-014
- Payment Data: FLOW-008 (properly tokenized via Stripe)
- Admin Credentials: FLOW-006 (login flow - needs auth bypass check)

Does this prioritization look correct? Should I proceed to Phase 5: Vulnerability Analysis for these 10 high-risk flows?
```

---

## Phase 5: Vulnerability Analysis

**Output Files**:
- `security-review/05-findings/README.md`
- `security-review/05-findings/FINDING-001.md` through `FINDING-010.md`

**Summary Presented to User**:
```
✅ Phase 5: Vulnerability Analysis - Complete

Total Findings: 10

Severity Breakdown:
- Critical: 2 findings
- High: 4 findings
- Medium: 3 findings
- Low: 1 finding

Top 3 Most Critical Findings:

1. FINDING-004: Command Injection in Stripe Webhook Handler
   - CVSS: 10.0 (Critical)
   - Risk Matrix: 25 (Critical)
   - Impact: Remote code execution, full server compromise
   - Exploitability: Moderate (requires Stripe webhook signature bypass or compromise)
   - PoC: Verified with test webhook event
   - Quick Win: LOW effort fix (use shellEscape or avoid exec entirely)

2. FINDING-001: SQL Injection in Product Search
   - CVSS: 9.8 (Critical)
   - Risk Matrix: 20 (Critical)
   - Impact: Complete database compromise, all PII exposed
   - Exploitability: Trivial (public endpoint, no authentication)
   - PoC: Verified with UNION-based attack
   - Quick Win: LOW effort fix (switch to parameterized query)

3. FINDING-002: Path Traversal in Admin File Upload
   - CVSS: 8.5 (High)
   - Risk Matrix: 16 (High)
   - Impact: Read/write arbitrary files, potential RCE if webshell uploaded
   - Exploitability: Easy (requires admin access but weak path sanitization)
   - PoC: Verified with ../../ traversal
   - Quick Win: MEDIUM effort (proper path validation + file type whitelist)

Quick Wins (Low Effort, High Impact):
- FINDING-004: Command Injection (2 hours to fix)
- FINDING-001: SQL Injection (1 hour to fix)
- FINDING-007: Missing HttpOnly on session cookies (15 minutes to fix)

Recommended Remediation Priorities:
1. IMMEDIATE (this week): FINDING-001, FINDING-004 (Critical, trivial exploits)
2. SHORT-TERM (this month): FINDING-002, FINDING-003, FINDING-006, FINDING-009 (High severity)
3. MEDIUM-TERM (next quarter): FINDING-005, FINDING-008, FINDING-010 (Medium severity)

Does this vulnerability analysis look complete? Should I proceed to Phase 6: Report Generation?
```

---

## Phase 6: Report Generation

**Output File**: `security-review/REPORT.md`

**Final Summary Presented to User**:
```
✅ Security Code Review Complete!

Report Location: security-review/REPORT.md

Executive Summary (excerpt):

This security review of MyWebApp E-commerce Platform identified 10 security
vulnerabilities across 5 OWASP Top 10 categories. Of these, 2 are rated Critical
and 4 are High severity, requiring immediate remediation. The most significant
risks include:

1. Command Injection in webhook handler (CVSS 10.0) - Remote code execution possible
2. SQL Injection in product search (CVSS 9.8) - Complete database compromise
3. Path Traversal in file upload (CVSS 8.5) - Arbitrary file read/write

Without remediation, these vulnerabilities could result in complete system compromise,
exposure of all 50,000 customer records (triggering GDPR breach notification),
potential fines up to €20M or 4% annual revenue, and significant reputational damage.

We recommend immediate remediation of Critical findings within 2 weeks, followed by
High severity fixes within 1 month.

Key Statistics:
- Total Findings: 10
- Critical: 2 (20%)
- High: 4 (40%)
- Medium: 3 (30%)
- Low: 1 (10%)

Immediate Actions Required (0-2 weeks):
- Fix SQL Injection in search API (FINDING-001)
- Fix Command Injection in webhook handler (FINDING-004)
- Implement rate limiting on public endpoints

Estimated Remediation Timeline:
- Immediate actions: 2 weeks
- Short-term (High severity): 1-2 months
- Medium-term (Medium severity): 3-6 months
- Long-term (architectural improvements): 6-12 months

Complete File Tree:

security-review/
├── 01-context.md                    (Business context & tech stack)
├── 02-assets.md                     (15 high-value assets identified)
├── 03-attack-surface.md             (47 entry points mapped)
├── 04-data-flows.md                 (23 flows analyzed, 10 high-risk)
├── 05-findings/
│   ├── README.md                    (Findings summary)
│   ├── FINDING-001.md               (SQL Injection - Critical)
│   ├── FINDING-002.md               (Path Traversal - High)
│   ├── FINDING-003.md               (Stored XSS - High)
│   ├── FINDING-004.md               (Command Injection - Critical)
│   ├── FINDING-005.md               (IDOR - Medium)
│   ├── FINDING-006.md               (CSRF - High)
│   ├── FINDING-007.md               (Session Security - Medium)
│   ├── FINDING-008.md               (Weak Password Policy - Medium)
│   ├── FINDING-009.md               (Missing Rate Limiting - High)
│   └── FINDING-010.md               (Verbose Error Messages - Low)
└── REPORT.md                        (Executive report - 23 pages)

Security code review complete. All artifacts are saved in security-review/.

You can now:
1. Review REPORT.md for executive summary and remediation roadmap
2. Share detailed findings with development team (05-findings/)
3. Begin remediation according to the prioritized timeline
4. Schedule follow-up review after critical fixes are deployed

Recommended next steps:
- Dev team kickoff meeting to review findings
- Assign owners for each finding
- Create tickets in issue tracker
- Schedule security review after remediation
```

---

## Sample Finding Detail (FINDING-001)

**File**: `security-review/05-findings/FINDING-001.md` (excerpt)

```markdown
# FINDING-001: SQL Injection in Product Search API

**Finding ID**: FINDING-001
**Title**: SQL Injection in Product Search API
**Severity (CVSS v3.1)**: Critical (9.8)
**Severity (Risk Matrix)**: Critical (20)
**CWE**: [CWE-89](https://cwe.mitre.org/data/definitions/89.html)
**OWASP Top 10**: A03:2021 - Injection
**Status**: Verified
**Date Found**: 2025-10-21

---

## Executive Summary

The product search API endpoint (`/api/search`) is vulnerable to SQL injection attacks
due to unsanitized user input being directly concatenated into SQL queries. An attacker
can exploit this to read, modify, or delete any data in the PostgreSQL database, including
all 50,000 customer records containing PII (emails, addresses, phone numbers).

**Business Impact**: Critical
**Exploitability**: Trivial (no authentication required)
**Asset(s) at Risk**: Customer PII database, product database, database credentials

---

## Vulnerability Details

### Description

The `/api/search` endpoint accepts a query parameter `q` containing user search terms.
This parameter is passed unsanitized to a raw SQL query using string interpolation:

```javascript
// models/product.js:67
const query = `SELECT * FROM products WHERE name LIKE '%${userQuery}%'`;
const results = await db.query(query);
```

An attacker can inject SQL commands by crafting malicious search terms, allowing:
- Data exfiltration via UNION-based injection
- Data modification or deletion via stacked queries
- Potential privilege escalation to database admin
- Operating system command execution (if PostgreSQL is misconfigured)

### Root Cause

The vulnerability exists because:
1. User input is trusted without validation
2. String interpolation is used instead of parameterized queries
3. No input sanitization or escaping is performed
4. The endpoint is public (no authentication required)

---

## Proof of Concept

### HTTP Request

```http
GET /api/search?q=test' UNION SELECT user, password, email, '1', '1' FROM users-- HTTP/1.1
Host: mywebapp.com
```

### Expected vs. Actual Behavior

**Expected** (Secure Behavior):
```
Returns products matching "test' UNION..."
(Treats entire string as literal search term)
```

**Actual** (Vulnerable Behavior):
```
Returns union of products + all user credentials:
{
  "results": [
    {"name": "Test Product", ...},
    {"name": "admin", "description": "$2b$10$abcd...", "category": "admin@example.com", ...},
    {"name": "user1", "description": "$2b$10$xyz...", "category": "user1@example.com", ...},
    ...
  ]
}
```

Successfully exfiltrates all usernames, password hashes, and emails from users table.

---

## Risk Assessment

### CVSS v3.1 Score

**Base Score**: 9.8 (Critical)
**Vector String**: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

**Metrics**:
- **Attack Vector (AV)**: Network - Remotely exploitable
- **Attack Complexity (AC)**: Low - No special conditions required
- **Privileges Required (PR)**: None - Public endpoint
- **User Interaction (UI)**: None - Fully automated
- **Scope (S)**: Unchanged - Impacts only the vulnerable application
- **Confidentiality Impact (C)**: High - All database data exposed
- **Integrity Impact (I)**: High - Attacker can modify any data
- **Availability Impact (A)**: High - Attacker can delete data or DoS

---

## Remediation

### Recommended Fix

Use parameterized queries (prepared statements) instead of string concatenation.

### Code Changes Required

**Before** (Vulnerable):
```javascript
// models/product.js:67
const userQuery = req.query.q;
const query = `SELECT * FROM products WHERE name LIKE '%${userQuery}%'`;
const results = await db.query(query);
```

**After** (Secure):
```javascript
// models/product.js:67
const userQuery = req.query.q;
const query = 'SELECT * FROM products WHERE name LIKE $1';
const results = await db.query(query, [`%${userQuery}%`]);
```

### Additional Security Measures

1. **Input validation**: Limit search term length, character whitelist
2. **Principle of least privilege**: Database user should have read-only access to products table
3. **WAF rules**: Deploy SQL injection detection patterns
4. **Monitoring**: Alert on suspicious query patterns in database logs

---
```

---

## How to Use This Skill

### Basic Invocation
```bash
# Analyze current directory
/secreview .

# Analyze specific directory with model selection
/secreview ./my-app --model gemini-2.5-pro

# Analyze with GPT-5 Pro
/secreview /path/to/app --model gpt-5-pro
```

### Expected Timeline
- Phase 1 (Discovery): 5-10 minutes
- Phase 2 (Assets): 5-10 minutes
- Phase 3 (Attack Surface): 10-15 minutes
- Phase 4 (Data Flows): 15-30 minutes (depending on app size)
- Phase 5 (Findings): 30-60 minutes (depending on number of high-risk flows)
- Phase 6 (Report): 5-10 minutes

**Total**: 1-2.5 hours for comprehensive security review

### What You Get

1. **Business Context** - Understanding of what the app does
2. **Asset Inventory** - What's valuable and needs protection
3. **Attack Surface Map** - All entry points and trust boundaries
4. **Data Flow Analysis** - Source-to-sink tracing with prioritization
5. **Detailed Findings** - Verified vulnerabilities with PoCs and CVSS scores
6. **Executive Report** - Business-focused summary with remediation roadmap

All artifacts saved as markdown for reuse, sharing, and incremental updates.
