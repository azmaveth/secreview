# Phase 1: Business Context Discovery

You are conducting the initial discovery phase of a comprehensive security code review. Your goal is to deeply understand the application's business purpose, architecture, and technology stack to inform later security analysis.

## Target Path
`{TARGET_PATH}`

## Objectives

1. **Understand Business Purpose**
   - What problem does this application solve?
   - Who are the users (internal/external, authenticated/anonymous)?
   - What are the core business workflows?
   - What business value does it provide?

2. **Map Application Architecture**
   - Application type (web app, API, mobile backend, CLI tool, desktop app, etc.)
   - Architecture pattern (monolith, microservices, serverless, etc.)
   - Component relationships and data flow at high level
   - Deployment model (cloud, on-prem, hybrid, edge)

3. **Identify Technology Stack**
   - Primary programming language(s) and versions
   - Web framework(s) (Express, Phoenix, Rails, Django, Spring, etc.)
   - Database(s) and data stores (PostgreSQL, MongoDB, Redis, etc.)
   - Authentication/authorization mechanisms (JWT, OAuth, sessions, etc.)
   - External service dependencies (payment processors, email services, cloud APIs)
   - Third-party libraries and dependencies (check package manifests)

4. **Understand Data Handling**
   - What types of data does the application process?
   - How is data stored, transmitted, and transformed?
   - What compliance requirements might apply (GDPR, HIPAA, PCI-DSS, etc.)?

## Analysis Process

### Step 1: Examine Documentation
Search for and analyze:
- `README.md`, `README`, `docs/` directory
- `ARCHITECTURE.md`, architecture diagrams (`.png`, `.svg`, `.drawio`)
- `API.md`, API documentation, OpenAPI/Swagger specs
- `CONTRIBUTING.md`, developer guides
- Inline code documentation, doc comments

### Step 2: Analyze Project Structure
Examine:
- Root directory structure and organization
- Configuration files (`.env.example`, `config/`, `*.yml`, `*.toml`, `*.json`)
- Entry points (`main.py`, `index.js`, `app.ex`, `Main.java`, etc.)
- Directory naming conventions that indicate features (e.g., `auth/`, `payments/`, `admin/`)

### Step 3: Inspect Dependency Manifests
Check:
- `package.json` (Node.js)
- `requirements.txt`, `Pipfile`, `pyproject.toml` (Python)
- `mix.exs` (Elixir)
- `Gemfile` (Ruby)
- `pom.xml`, `build.gradle` (Java)
- `Cargo.toml` (Rust)
- `go.mod` (Go)

Look for security-relevant dependencies:
- Authentication/authorization libraries
- Database drivers
- HTTP clients
- Cryptography libraries
- File upload handlers
- Template engines

### Step 4: Identify Routes and Entry Points
Scan for:
- Route definitions (Express routes, Phoenix routers, Rails routes, Django URLs)
- API endpoints (REST, GraphQL, gRPC)
- WebSocket/Phoenix Channel handlers
- CLI command definitions
- Background job handlers

### Step 5: Examine Configuration
Review:
- Environment variable usage (`.env.example`, config files)
- Database connection configurations
- External service integrations (API keys, webhooks)
- Security settings (CORS, CSP, session config, cookie settings)
- Logging and monitoring configurations

## Output Format

Create a markdown file with the following structure:

```markdown
# Security Review - Business Context Discovery

**Application Name**: [Name]
**Review Date**: [Date]
**Reviewer**: Claude Security Analysis Skill
**Target Path**: [Path]

---

## Executive Summary

[2-3 paragraph overview of what this application does, who uses it, and its business value]

---

## Application Profile

### Application Type
- **Category**: [Web Application / API / Mobile Backend / CLI Tool / Desktop App / etc.]
- **Architecture**: [Monolith / Microservices / Serverless / etc.]
- **Deployment**: [Cloud / On-Prem / Hybrid / Edge]
- **User Base**: [Internal / External / Public / B2B / B2C]

### Business Purpose
[Detailed description of business purpose and value proposition]

### Core Features
1. [Feature 1 - description]
2. [Feature 2 - description]
3. [Feature 3 - description]
...

### Key Business Workflows
1. **[Workflow Name]**: [Description of workflow from start to finish]
2. **[Workflow Name]**: [Description of workflow from start to finish]
...

---

## Technology Stack

### Languages & Frameworks
- **Primary Language**: [Language] [Version]
- **Web Framework**: [Framework] [Version]
- **Other Languages**: [If multi-language]

### Data Layer
- **Primary Database**: [Database] [Version]
- **Caching**: [Redis / Memcached / etc.]
- **Search**: [Elasticsearch / etc.]
- **Message Queue**: [RabbitMQ / Kafka / etc.]
- **File Storage**: [S3 / Local / etc.]

### Authentication & Authorization
- **Authentication Method**: [JWT / Sessions / OAuth2 / SAML / etc.]
- **Authorization Pattern**: [RBAC / ABAC / ACL / etc.]
- **Identity Provider**: [Auth0 / Cognito / Custom / etc.]
- **Session Management**: [Cookie-based / Token-based / etc.]

### External Integrations
1. **[Service Name]**: [Purpose] - [API type]
2. **[Service Name]**: [Purpose] - [API type]
...

### Key Dependencies
[List critical third-party libraries, especially security-relevant ones]

| Dependency | Version | Purpose | Security Notes |
|------------|---------|---------|----------------|
| [Name] | [Version] | [Purpose] | [Known vulns or security relevance] |
| ... | ... | ... | ... |

---

## Application Architecture

### Component Overview
[Describe major components and their relationships]

```
[ASCII diagram or description of architecture]
Example:
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Client    │─────▶│  API Server │─────▶│  Database   │
└─────────────┘      └─────────────┘      └─────────────┘
                            │
                            ▼
                     ┌─────────────┐
                     │  Redis Cache│
                     └─────────────┘
```

### Entry Points
[List all identified entry points where external world interacts with the app]

1. **HTTP Endpoints**: [Routes file location]
   - Public endpoints: [Count]
   - Authenticated endpoints: [Count]

2. **WebSocket/Channels**: [If applicable]

3. **CLI Commands**: [If applicable]

4. **Background Jobs**: [If applicable]

5. **Other**: [APIs, file watchers, etc.]

### Data Flow Overview
[High-level description of how data moves through the system]

---

## Data Handling

### Data Types Processed
- **User Data**: [PII, credentials, preferences, etc.]
- **Business Data**: [Financial, transactional, operational, etc.]
- **System Data**: [Logs, metrics, configuration, etc.]

### Data Storage
- **Persistent Storage**: [Database tables, file systems]
- **Temporary Storage**: [Cache, sessions, temp files]
- **External Storage**: [S3, CDN, etc.]

### Data Transmission
- **External APIs**: [What data is sent where]
- **Client Communication**: [HTTP, WebSocket, etc.]
- **Inter-Service Communication**: [If microservices]

### Compliance Considerations
[Identify potential compliance requirements based on data types]
- [ ] GDPR (EU personal data)
- [ ] HIPAA (Health information)
- [ ] PCI-DSS (Payment card data)
- [ ] SOC 2 (Service organization controls)
- [ ] CCPA (California consumer privacy)
- [ ] Other: [Specify]

---

## Security-Relevant Observations

### Positive Security Findings
[List security-positive patterns observed during discovery]
- [Example: Uses parameterized queries throughout]
- [Example: Implements rate limiting on authentication endpoints]
...

### Areas of Interest for Deeper Analysis
[Note areas that warrant closer security scrutiny in later phases]
- [Example: Custom authentication implementation rather than proven library]
- [Example: File upload functionality without visible validation]
...

### Missing Documentation
[Note where documentation is lacking]
- [Example: No architecture diagram found]
- [Example: API endpoints not documented]
...

---

## Next Steps

Based on this discovery phase, the following areas will be prioritized in subsequent analysis phases:

1. **Asset Identification**: [Key assets to focus on based on business purpose]
2. **Attack Surface**: [Entry points requiring detailed enumeration]
3. **Data Flows**: [Critical flows to trace from source to sink]

---

## Appendix

### Files Analyzed
[List key files examined during discovery]
- `[filepath]` - [Purpose]
- `[filepath]` - [Purpose]
...

### References
[Links to external documentation, repos, or resources referenced]
- [Link 1]
- [Link 2]
...
```

## Analysis Instructions

1. **Be thorough**: Read documentation, examine structure, analyze dependencies
2. **Be accurate**: Only include information you can verify from the codebase
3. **Be specific**: Include file paths, version numbers, exact names
4. **Focus on security relevance**: Emphasize aspects that matter for security analysis
5. **Use glob and grep**: Search efficiently for patterns across the codebase
6. **Read key files**: Actually read README, config files, route definitions
7. **Infer intelligently**: If documentation is sparse, infer from code structure and naming
8. **Note gaps**: Document where information is missing or unclear

## Tools to Use

- `Glob`: Find files by pattern (e.g., `**/*route*.{js,py,ex}`, `**/README.md`)
- `Grep`: Search for patterns (e.g., authentication mechanisms, database connections)
- `Read`: Read documentation, config files, entry points
- Do NOT use `Task` or external AI tools - perform this analysis directly

## Completion Criteria

The analysis is complete when:
- ✅ Business purpose is clearly understood
- ✅ All major components are identified
- ✅ Technology stack is documented with versions
- ✅ Entry points are enumerated
- ✅ Data types and flows are understood at high level
- ✅ Output file `security-review/01-context.md` is created

## After Completion

Present a summary to the user:
- Application name and purpose (2 sentences)
- Technology stack (language, framework, database)
- Number of entry points identified
- Key security-relevant observations (2-3 bullet points)

Ask user: "Does this context analysis look accurate? Should I proceed to Phase 2: Asset Identification?"
