# Phase 2: Asset Identification

You are conducting the asset identification phase of a comprehensive security code review. Your goal is to identify all valuable assets within the application that would be attractive targets from an attacker's perspective.

## Input
- **Business Context**: Read `security-review/01-context.md` to understand the application
- **Target Path**: `{TARGET_PATH}`

## Objectives

1. **Identify Sensitive Data Assets**
   - User credentials (passwords, API keys, tokens)
   - Personally Identifiable Information (PII)
   - Financial data (credit cards, bank accounts, payment info)
   - Health information (PHI, medical records)
   - Business-critical data (trade secrets, IP, contracts)
   - Session tokens and authentication artifacts

2. **Identify System Assets**
   - Administrative access and privileges
   - Database credentials and connection strings
   - API keys and service credentials
   - Encryption keys and certificates
   - Configuration secrets
   - Infrastructure access (AWS keys, SSH keys, etc.)

3. **Identify Access and Permissions**
   - Privilege levels (admin, user, guest, service accounts)
   - Authorization boundaries
   - Elevated permissions and dangerous capabilities
   - Inter-service trust relationships

4. **Identify External Integrations**
   - Payment processors (Stripe, PayPal, etc.)
   - Email services (SendGrid, Mailgun, etc.)
   - SMS/Communication services (Twilio, etc.)
   - Cloud services (AWS, GCP, Azure)
   - Third-party APIs
   - Analytics and tracking services

5. **Attacker Value Assessment**
   - What would an external attacker want to steal?
   - What would an insider threat target?
   - What could be used for lateral movement?
   - What has compliance/regulatory value?
   - What has reputational impact if breached?

## Analysis Process

### Step 1: Review Business Context
- Re-read `security-review/01-context.md`
- Understand what data the application handles based on its business purpose
- Identify user types and their data

### Step 2: Examine Data Models
Search for and analyze:
- Database schema files (`migrations/`, `schema.rb`, `models.py`, `*.sql`)
- ORM model definitions (`models/`, `schema/`, entity classes)
- Data validation rules
- Sensitive field names (password, email, ssn, credit_card, token, secret, key, etc.)

### Step 3: Analyze Authentication & Authorization
Examine:
- User authentication mechanisms (how users prove identity)
- Session management (where/how sessions are stored)
- Password storage (hashing algorithms, salt usage)
- API key/token management
- OAuth flows and token handling
- Role/permission definitions (RBAC models, permission constants)
- Authorization checks and middleware

### Step 4: Search for Secrets and Credentials
Look for:
- Environment variable definitions (`.env.example`, config files)
- Hardcoded secrets (grep for "password", "api_key", "secret", "token")
- Credential storage mechanisms
- Secret management services (Vault, AWS Secrets Manager, etc.)
- Certificate and key storage

### Step 5: Identify External Service Integrations
Find:
- API client configurations
- Payment processing code
- Email sending logic
- Cloud service SDK usage (AWS SDK, Google Cloud, etc.)
- Webhook handlers
- Third-party authentication providers

### Step 6: Analyze Access Control
Map:
- Admin interfaces and privileged functions
- User roles and capabilities
- Permission checking functions
- Dangerous operations (user deletion, data export, system commands)
- Multi-tenancy boundaries (if applicable)

### Step 7: Assess Attacker Motivation
For each identified asset, consider:
- **Monetary value**: Can it be sold or monetized?
- **Compliance impact**: Would breach trigger regulatory fines?
- **Reputational damage**: Would leak harm the business?
- **Lateral movement**: Can it be used to access other systems?
- **Privilege escalation**: Can it grant higher access?

## Output Format

Create a markdown file with the following structure:

```markdown
# Security Review - Asset Identification

**Application**: [Name from context]
**Phase**: Asset Identification
**Date**: [Date]
**Input**: security-review/01-context.md

---

## Executive Summary

[2-3 sentences describing the most valuable assets in this application from an attacker's perspective]

---

## Asset Inventory

### Crown Jewels (Highest Value Assets)

[Rank top 5-10 assets by attacker value]

| Rank | Asset | Type | Location | Attacker Value | Business Impact if Compromised |
|------|-------|------|----------|----------------|-------------------------------|
| 1 | [Asset name] | [Data/Credential/Access] | [Where stored/used] | [Why valuable] | [Impact description] |
| 2 | ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... | ... |

---

## Detailed Asset Analysis

### 1. Sensitive Data Assets

#### 1.1 User Credentials
[Details about how credentials are stored and managed]

- **Password Storage**
  - Location: `[filepath:line]`
  - Hashing Algorithm: [bcrypt / Argon2 / PBKDF2 / MD5 / plain text / etc.]
  - Salt: [Per-user / Global / None]
  - Security Assessment: [Strong / Weak / Vulnerable]

- **API Keys/Tokens**
  - Storage: [Database / Environment / Config files]
  - Location: `[filepath:line]`
  - Encryption: [Yes / No]
  - Rotation: [Supported / Not supported]

- **Session Tokens**
  - Storage: [Cookie / LocalStorage / Database]
  - HttpOnly: [Yes / No]
  - Secure flag: [Yes / No]
  - SameSite: [Strict / Lax / None]
  - Expiration: [Duration]

#### 1.2 Personally Identifiable Information (PII)
[List all PII fields found in the application]

| PII Type | Fields | Storage Location | Access Control | Encryption |
|----------|--------|------------------|----------------|------------|
| Email | `users.email` | `[table/collection]` | [Who can access] | [At rest / In transit / None] |
| Phone | `users.phone` | `[table/collection]` | [Who can access] | [At rest / In transit / None] |
| Address | `users.address` | `[table/collection]` | [Who can access] | [At rest / In transit / None] |
| ... | ... | ... | ... | ... |

**Compliance Requirements**: [GDPR / CCPA / HIPAA / None identified]

#### 1.3 Financial Data
[If application handles financial data]

| Data Type | Fields | Storage | PCI-DSS Scope | Security Controls |
|-----------|--------|---------|---------------|-------------------|
| Credit Cards | `[fields]` | `[location]` | [In scope / Out of scope] | [Tokenization / Encryption / None] |
| Bank Accounts | `[fields]` | `[location]` | N/A | [Controls] |
| Transactions | `[fields]` | `[location]` | [In scope / Out of scope] | [Controls] |

#### 1.4 Business-Critical Data
[Application-specific valuable business data]

- **[Data Type 1]**
  - Description: [What it is]
  - Location: `[filepath:line]`
  - Access Control: [Who can access]
  - Business Value: [Why it matters]

- **[Data Type 2]**
  - [Same format]

#### 1.5 Health Information (if applicable)
[PHI, medical records, health data]

**HIPAA Applicability**: [Yes / No / Unknown]

---

### 2. System Assets

#### 2.1 Database Credentials
[How database access is secured]

- **Connection Strings**
  - Location: `[filepath:line]`
  - Storage Method: [Environment variables / Config file / Hardcoded]
  - Secrets Management: [Vault / AWS Secrets / None]
  - Database Users: [List users and their privileges]

#### 2.2 External Service Credentials
[API keys, service accounts, etc.]

| Service | Credential Type | Storage Location | Permissions Granted | Risk if Compromised |
|---------|----------------|------------------|---------------------|---------------------|
| [AWS] | [Access Key] | `[location]` | [IAM permissions] | [Impact] |
| [Stripe] | [API Key] | `[location]` | [Payment processing] | [Impact] |
| [SendGrid] | [API Key] | `[location]` | [Email sending] | [Impact] |
| ... | ... | ... | ... | ... |

#### 2.3 Encryption Keys & Certificates
[Cryptographic material]

- **Encryption Keys**
  - Location: `[filepath:line]`
  - Purpose: [What they protect]
  - Key Management: [Rotation / Backup / Escrow]

- **TLS Certificates**
  - Location: `[filepath:line]`
  - Expiration: [Date or "Unknown"]
  - Certificate Authority: [CA name]

#### 2.4 Signing Keys & Secrets
[JWT secrets, HMAC keys, etc.]

- **JWT Secret**
  - Location: `[filepath:line]`
  - Algorithm: [HS256 / RS256 / etc.]
  - Rotation: [Supported / Not supported]

- **Other Signing Keys**
  - [List with details]

---

### 3. Access & Privilege Assets

#### 3.1 Privilege Levels
[User roles and capabilities]

| Role | Capabilities | Population | Elevation Path | Risk |
|------|--------------|------------|----------------|------|
| Admin | [List capabilities] | [# of users] | [How to become admin] | [High/Medium/Low] |
| User | [List capabilities] | [# of users] | [How to become user] | [Risk level] |
| Guest | [List capabilities] | [# of users] | N/A | [Risk level] |
| ... | ... | ... | ... | ... |

#### 3.2 Dangerous Operations
[Operations that could cause significant damage]

| Operation | Who Can Execute | Authorization Check | Audit Logging | Risk |
|-----------|----------------|---------------------|---------------|------|
| Delete user | `[role]` | `[filepath:line]` | [Yes/No] | [High/Medium/Low] |
| Export all data | `[role]` | `[filepath:line]` | [Yes/No] | [Risk] |
| Execute commands | `[role]` | `[filepath:line]` | [Yes/No] | [Risk] |
| ... | ... | ... | ... | ... |

#### 3.3 Service Accounts & Machine Identities
[Non-human accounts with system access]

- **[Service Account Name]**
  - Purpose: [What it does]
  - Permissions: [What it can access]
  - Authentication: [How it authenticates]
  - Risk: [If compromised]

---

### 4. External Integration Assets

#### 4.1 Payment Processing
[If applicable]

- **Provider**: [Stripe / PayPal / etc.]
- **Integration Type**: [Hosted / Direct API / etc.]
- **Sensitive Data Exposure**: [What data is sent]
- **Credentials**: [Where stored - reference System Assets section]

#### 4.2 Authentication Providers
[OAuth, SAML, social login]

- **Provider**: [Google / GitHub / Auth0 / etc.]
- **Protocol**: [OAuth2 / SAML / OIDC]
- **Trust Model**: [How trust is established]
- **Credential Storage**: [Where tokens/assertions are kept]

#### 4.3 Cloud Services
[AWS, GCP, Azure integrations]

- **Provider**: [AWS / GCP / Azure]
- **Services Used**: [S3, Lambda, etc.]
- **Permissions**: [IAM roles and policies]
- **Credential Storage**: [Where stored]

#### 4.4 Communication Services
[Email, SMS, push notifications]

| Service | Provider | Purpose | Data Sent | Credential Location |
|---------|----------|---------|-----------|---------------------|
| Email | [SendGrid] | [Transactional email] | [PII in emails?] | `[filepath:line]` |
| SMS | [Twilio] | [2FA codes] | [Phone numbers] | `[filepath:line]` |
| ... | ... | ... | ... | ... |

---

## Attacker Value Assessment

### High-Value Targets (Attacker Perspective)

#### External Attacker
[What would an internet-based attacker target?]

1. **[Asset Name]**
   - Attacker Goal: [Data theft / Account takeover / Financial fraud / etc.]
   - Access Path: [How they would try to reach it]
   - Monetization: [How they would profit]
   - Likelihood: [High / Medium / Low]

2. **[Asset Name]**
   - [Same format]

#### Insider Threat
[What would a malicious employee or contractor target?]

1. **[Asset Name]**
   - Insider Advantage: [What access they already have]
   - Motivation: [Financial / Espionage / Sabotage]
   - Detection Difficulty: [Easy / Hard to detect]

#### Competitor/Nation-State
[If applicable - IP theft, espionage]

1. **[Asset Name]**
   - Strategic Value: [Why a sophisticated attacker would want it]
   - Difficulty: [Technical sophistication required]

---

## Asset Protection Summary

### Well-Protected Assets
[Assets with strong security controls]

- **[Asset Name]**
  - Protection Mechanisms: [Encryption, access control, monitoring, etc.]
  - Location: `[filepath:line]`
  - Assessment: [Why it's well protected]

### At-Risk Assets
[Assets with weak or missing protections]

- **[Asset Name]**
  - Current Protection: [What exists]
  - Gaps: [What's missing]
  - Risk Level: [Critical / High / Medium]
  - Location: `[filepath:line]`

---

## Prioritization for Attack Surface Analysis

Based on this asset inventory, the following assets should be prioritized in Phase 3 (Attack Surface Enumeration) and Phase 4 (Data Flow Analysis):

1. **[Asset Name]** - [Reason for prioritization]
2. **[Asset Name]** - [Reason]
3. **[Asset Name]** - [Reason]
...

---

## Appendix

### Database Tables/Collections Analyzed
- `[table_name]` - [Description]
- `[table_name]` - [Description]

### Files Analyzed
- `[filepath]` - [Purpose]
- `[filepath]` - [Purpose]

### Search Patterns Used
- Searched for: `[pattern]` - Found: [count] matches
- Searched for: `[pattern]` - Found: [count] matches
```

## Analysis Instructions

1. **Read the business context**: Understand what the app does to know what assets to look for
2. **Search systematically**: Use Glob and Grep to find models, configs, authentication code
3. **Be specific**: Include file paths and line numbers for every asset found
4. **Think like an attacker**: Consider what has value on the dark web, for competitors, or for privilege escalation
5. **Map relationships**: Connect assets to access controls, encryption, and business value
6. **Assess protection**: Note which assets are well-protected vs. vulnerable
7. **Prioritize**: Rank assets by combination of attacker value + business impact

## Tools to Use

- `Read`: Read `security-review/01-context.md` first, then model files, config files
- `Glob`: Find models (`**/*model*.{py,js,rb,ex}`), migrations, schema files
- `Grep`: Search for "password", "secret", "api_key", "token", "credential", role names
- Do NOT use external AI tools - perform analysis directly

## Completion Criteria

The analysis is complete when:
- ✅ All data models have been examined
- ✅ Authentication and authorization mechanisms are documented
- ✅ External service integrations are identified
- ✅ Crown jewels are ranked by attacker value
- ✅ At-risk assets are flagged
- ✅ Output file `security-review/02-assets.md` is created

## After Completion

Present a summary to the user:
- Total number of high-value assets identified
- Top 3 crown jewels
- Number of at-risk assets requiring attention
- Key concerns (2-3 bullet points)

Ask user: "Would you like to add any additional assets to this inventory, or should I proceed to Phase 3: Attack Surface Enumeration?"
