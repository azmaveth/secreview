---
name: Security Code Review
description: Comprehensive security-focused code review using source-to-sink methodology. Analyzes business context, identifies valuable assets, enumerates attack surface, traces data flows, and generates detailed vulnerability findings with CVSS scoring and remediation guidance. Use this skill when the user requests a security audit, vulnerability assessment, or security review of a codebase.
---

# Security Code Review Skill

**Skill Name**: `secreview`

**Description**: Comprehensive security-focused code review using source-to-sink methodology. Analyzes business context, identifies valuable assets, enumerates attack surface, traces data flows, and generates detailed vulnerability findings with CVSS scoring and remediation guidance.

**Usage**: `/secreview [path] [--model model-name]`

---

## Overview

This skill performs a multi-phase security analysis with interactive checkpoints:

1. **Business Context Discovery** - Understand app purpose and architecture
2. **Asset Identification** - Identify valuable targets from attacker perspective
3. **Attack Surface Enumeration** - Map all external interaction points
4. **Data Flow Analysis** - Trace sources to sinks, prioritize by asset intersection
5. **Vulnerability Analysis** - Deep dive on high-risk flows, generate findings
6. **Report Generation** - Executive summary with detailed findings

All intermediate results are saved as markdown artifacts in `security-review/` directory for reuse and incremental analysis.

---

## Skill Execution Flow

### Prerequisites Check
1. Verify target path exists
2. Detect programming language(s) and frameworks
3. Ask user to select AI model if not provided via `--model` flag
4. Create `security-review/` output directory

### Phase 1: Business Context Discovery
**Prompt**: `@.claude/skills/secreview/prompts/01-discovery.md`
**Output**: `security-review/01-context.md`

Analyze:
- README, documentation, architecture diagrams
- Application purpose, features, business workflows
- Technology stack (languages, frameworks, dependencies)
- Deployment model (web, mobile, API, CLI, etc.)

**Checkpoint**: Present summary, ask user to review before continuing.

### Phase 2: Asset Identification
**Prompt**: `@.claude/skills/secreview/prompts/02-assets.md`
**Input**: `security-review/01-context.md`
**Output**: `security-review/02-assets.md`

Identify:
- Sensitive data types (PII, credentials, financial, health, etc.)
- Privilege levels and access control mechanisms
- External integrations (databases, APIs, auth providers)
- Crown jewels from attacker perspective

**Checkpoint**: Present assets, ask user to review/add high-value targets.

### Phase 3: Attack Surface Enumeration
**Prompt**: `@.claude/skills/secreview/prompts/03-surface.md`
**Input**: `security-review/01-context.md`, `security-review/02-assets.md`
**Output**: `security-review/03-attack-surface.md`

Map:
- Entry points (HTTP routes, WebSocket channels, GraphQL endpoints, CLI args, etc.)
- Authentication/authorization boundaries
- External data inputs (forms, file uploads, API requests)
- Trust boundaries

**Checkpoint**: Present attack surface, confirm completeness.

### Phase 4: Data Flow Analysis
**Prompt**: `@.claude/skills/secreview/prompts/04-flows.md`
**Input**: All previous phase outputs
**Output**: `security-review/04-data-flows.md`

Trace:
- Sources: user input, external APIs, file reads, environment variables
- Sinks: database writes, file writes, external API calls, command execution, templates/views
- Validation/sanitization checkpoints
- Flows intersecting high-value assets (prioritized for Phase 5)

**Checkpoint**: Present top 10 highest-risk flows for deep analysis.

### Phase 5: Vulnerability Analysis
**Prompt**: `@.claude/skills/secreview/prompts/05-findings.md`
**Input**: All previous phase outputs + prioritized flows
**Output**: `security-review/05-findings/FINDING-{ID}.md` (one per vulnerability)

For each high-risk flow:
- Analyze for OWASP Top 10 and language-specific vulnerabilities
- Map to CWE(s)
- Create proof-of-concept exploit (where applicable)
- Calculate CVSS v3.1 score
- Calculate Impact × Likelihood risk rating
- Provide remediation recommendations with code examples

**Checkpoint**: Present findings summary, allow review before report generation.

### Phase 6: Report Generation
**Prompt**: `@.claude/skills/secreview/prompts/06-report.md`
**Input**: All previous phase outputs
**Output**: `security-review/REPORT.md`

Generate:
- Executive summary with key metrics
- Risk overview (findings by severity)
- Detailed findings (reference individual markdown files)
- Remediation roadmap (quick wins vs. long-term improvements)
- Compliance considerations (if applicable)

**Final Output**: Present report location and summary statistics.

---

## Output Structure

```
security-review/
├── 01-context.md           # Business context & tech stack
├── 02-assets.md            # Valuable assets & targets
├── 03-attack-surface.md    # Entry points & boundaries
├── 04-data-flows.md        # Source-to-sink flows
├── 05-findings/            # Individual vulnerability reports
│   ├── FINDING-001.md
│   ├── FINDING-002.md
│   └── ...
└── REPORT.md               # Executive summary & consolidated report
```

---

## Risk Scoring Methodology

### CVSS v3.1 Calculation
Calculate base score using:
- **Attack Vector** (Network/Adjacent/Local/Physical)
- **Attack Complexity** (Low/High)
- **Privileges Required** (None/Low/High)
- **User Interaction** (None/Required)
- **Scope** (Unchanged/Changed)
- **Confidentiality Impact** (None/Low/High)
- **Integrity Impact** (None/Low/High)
- **Availability Impact** (None/Low/High)

Severity ranges:
- **Critical**: 9.0-10.0
- **High**: 7.0-8.9
- **Medium**: 4.0-6.9
- **Low**: 0.1-3.9
- **Info**: 0.0

### Impact × Likelihood Matrix

**Impact Scale** (1-5):
1. Informational - No direct business impact
2. Low - Minor inconvenience or data exposure
3. Medium - Significant data exposure or service disruption
4. High - Major data breach or extended outage
5. Critical - Complete system compromise or catastrophic data loss

**Likelihood Scale** (1-5):
1. Very Low - Requires exceptional circumstances
2. Low - Difficult to exploit, requires specific conditions
3. Medium - Moderately difficult, some prerequisites
4. High - Easy to exploit with minimal prerequisites
5. Very High - Trivial to exploit, publicly known

**Risk Rating** = Impact × Likelihood:
- **Critical**: 20-25
- **High**: 12-19
- **Medium**: 6-11
- **Low**: 2-5
- **Info**: 1

---

## Language-Specific Security Patterns

The skill automatically detects and applies language-specific security checks:

### Web Applications (All Languages)
- SQL Injection (CWE-89)
- XSS (CWE-79)
- CSRF (CWE-352)
- Path Traversal (CWE-22)
- Insecure Deserialization (CWE-502)
- Authentication/Authorization bypasses (CWE-284, CWE-287)
- Sensitive Data Exposure (CWE-200, CWE-311)
- Security Misconfiguration (CWE-16)
- XXE (CWE-611)
- SSRF (CWE-918)

### JavaScript/TypeScript
- Prototype Pollution (CWE-1321)
- `eval()` and code injection (CWE-95)
- Regex DoS (CWE-1333)
- JWT vulnerabilities (CWE-347, CWE-259)
- NPM dependency vulnerabilities

### Python
- Pickle deserialization (CWE-502)
- OS command injection via `os.system`, `subprocess` (CWE-78)
- Template injection (Jinja2, Django) (CWE-94)
- SQL injection (raw queries, f-strings in SQL) (CWE-89)

### Elixir/Phoenix
- Ecto SQL injection (raw fragments, dynamic queries) (CWE-89)
- Plug CSRF bypass (CWE-352)
- Phoenix LiveView XSS (raw HTML injection) (CWE-79)
- Insecure channel authentication (CWE-306)
- Atom exhaustion DoS (CWE-400)

### Ruby/Rails
- Mass assignment (CWE-915)
- SQL injection (raw SQL, `find_by_sql`) (CWE-89)
- Remote code execution via YAML (CWE-94)
- Template injection (ERB) (CWE-94)

### Java/Kotlin
- Insecure deserialization (CWE-502)
- XML External Entity (XXE) (CWE-611)
- Path traversal (CWE-22)
- Spring Security misconfigurations (CWE-16)

### Go
- SQL injection (string concatenation in queries) (CWE-89)
- Command injection (exec.Command with unsanitized input) (CWE-78)
- Path traversal (filepath.Join misuse) (CWE-22)
- Race conditions (CWE-race)

### Rust
- Unsafe block vulnerabilities (CWE-119)
- FFI boundary issues (CWE-704)
- Integer overflow in unsafe contexts (CWE-190)

---

## Model Selection

When invoked, the skill will:
1. Check if `--model` flag was provided
2. If not, prompt user with available models using `mcp__zen__listmodels`
3. Recommend `gemini-2.5-pro` for large codebases (1M context)
4. Recommend `gpt-5-pro` for balanced analysis (400K context)

User can override at any phase by specifying model preference.

---

## Example Invocation

```bash
# Analyze current directory with Gemini 2.5 Pro
/secreview . --model gemini-2.5-pro

# Analyze specific application directory (model will be prompted)
/secreview ../my-web-app

# Analyze with GPT-5 Pro
/secreview ./api-server --model gpt-5-pro
```

---

## Notes

- **Incremental Analysis**: Each phase saves output, allowing resume from any checkpoint
- **Reusable Artifacts**: Markdown outputs can be refined manually and reused
- **Interactive Workflow**: User reviews and approves between phases
- **No False Positives Focus**: Findings must be verified and exploitable
- **Remediation First**: Every finding includes actionable fix with code examples
- **Compliance Mapping**: Where applicable, map findings to compliance requirements (OWASP, PCI-DSS, HIPAA, etc.)

---

## Skill Maintenance

To update security patterns:
1. Edit language-specific checks in `prompts/05-findings.md`
2. Add new CWE mappings to finding template
3. Update CVSS calculator logic if CVSS spec changes
4. Add new language support by extending detection and patterns

---

## References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [CVSS v3.1 Calculator](https://www.first.org/cvss/calculator/3.1)
- [OWASP Code Review Guide](https://owasp.org/www-project-code-review-guide/)
