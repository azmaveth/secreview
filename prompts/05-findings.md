# Phase 5: Vulnerability Analysis & Findings Generation

You are conducting deep vulnerability analysis on prioritized data flows. Your goal is to verify exploitability, create proof-of-concept exploits, calculate risk scores (CVSS and Impact×Likelihood), map to CWE, and provide remediation guidance. Each verified vulnerability becomes a formal finding.

## Inputs
- **All Previous Phases**: Read all prior outputs for context
- **Prioritized Flows**: From `security-review/04-data-flows.md`
- **Target Path**: `{TARGET_PATH}`
- **Selected Model**: `{MODEL}` (for vulnerability analysis assistance)

## Objectives

1. **Analyze High-Risk Flows for Vulnerabilities**
   - OWASP Top 10 (2021)
   - CWE Top 25
   - Language/framework-specific vulnerabilities
   - Business logic flaws
   - Authorization bypasses

2. **Verify Exploitability**
   - Confirm vulnerability is real (not false positive)
   - Test if exploit is actually possible
   - Determine prerequisites for exploitation
   - Assess reliability of exploit

3. **Create Proof-of-Concept Exploits**
   - HTTP requests (curl commands, raw HTTP)
   - Code snippets demonstrating attack
   - Step-by-step exploit instructions
   - Expected vs. actual behavior

4. **Calculate Risk Scores**
   - **CVSS v3.1**: Full base score calculation
   - **Impact × Likelihood**: Simplified risk matrix
   - Severity classification (Critical/High/Medium/Low/Info)

5. **Map to Security Standards**
   - CWE (Common Weakness Enumeration)
   - OWASP Top 10 category
   - Compliance impact (GDPR, HIPAA, PCI-DSS if applicable)

6. **Provide Remediation Guidance**
   - Root cause explanation
   - Secure coding pattern (with code example)
   - Framework-specific recommendations
   - Validation and testing guidance

## Vulnerability Categories to Check

### OWASP Top 10 (2021)

1. **A01:2021 – Broken Access Control**
   - Insecure Direct Object Reference (IDOR)
   - Missing function-level access control
   - Privilege escalation
   - Forced browsing

2. **A02:2021 – Cryptographic Failures**
   - Sensitive data transmitted in clear text
   - Weak encryption algorithms
   - Hardcoded cryptographic keys
   - Missing encryption at rest

3. **A03:2021 – Injection**
   - SQL Injection (CWE-89)
   - Command Injection (CWE-78)
   - LDAP Injection (CWE-90)
   - XPath Injection (CWE-643)
   - Template Injection (CWE-94)
   - NoSQL Injection

4. **A04:2021 – Insecure Design**
   - Business logic flaws
   - Missing rate limiting
   - Insufficient threat modeling

5. **A05:2021 – Security Misconfiguration**
   - Default credentials
   - Unnecessary features enabled
   - Verbose error messages
   - Missing security headers

6. **A06:2021 – Vulnerable and Outdated Components**
   - Dependencies with known CVEs
   - Unmaintained libraries
   - Missing security patches

7. **A07:2021 – Identification and Authentication Failures**
   - Weak password requirements
   - Credential stuffing vulnerability
   - Session fixation
   - Missing multi-factor authentication

8. **A08:2021 – Software and Data Integrity Failures**
   - Insecure deserialization (CWE-502)
   - Unsigned code execution
   - Auto-update without verification

9. **A09:2021 – Security Logging and Monitoring Failures**
   - Missing audit logs
   - Insufficient logging of security events
   - No alerting on suspicious activity

10. **A10:2021 – Server-Side Request Forgery (SSRF)**
    - Unvalidated URL parameters
    - Access to internal services
    - Cloud metadata service access

### Language-Specific Vulnerabilities

Refer to the language-specific security patterns in `skill.md`.

## Analysis Process

### Step 1: Review Prioritized Flows
- Read `security-review/04-data-flows.md`
- Extract top 10-15 flows marked for analysis
- Understand each flow's source, sink, and current validation

### Step 2: Analyze Each Flow for Vulnerabilities

For each prioritized flow:

#### 2.1 Read the Relevant Code
- Read all files in the flow path
- Understand the complete logic
- Identify all transformation and validation steps

#### 2.2 Identify Vulnerability Type
- What category of vulnerability might exist?
- What is the attack vector?
- What CWE(s) apply?

#### 2.3 Verify Exploitability
- Can attacker-controlled data reach the sink?
- Are there bypasses for existing validation?
- What are the prerequisites for exploitation?
- Test the attack mentally or with code analysis

#### 2.4 Create Proof-of-Concept
- Write concrete exploit (HTTP request, code snippet, etc.)
- Document expected impact
- Note any conditions required

#### 2.5 Assess Impact
- Confidentiality: What data can be accessed/stolen?
- Integrity: What data can be modified/deleted?
- Availability: Can the system be disrupted?
- Scope: Can the attack affect other users/systems?

#### 2.6 Calculate CVSS v3.1 Score

**Base Metrics**:
- **Attack Vector (AV)**: Network (N) / Adjacent (A) / Local (L) / Physical (P)
- **Attack Complexity (AC)**: Low (L) / High (H)
- **Privileges Required (PR)**: None (N) / Low (L) / High (H)
- **User Interaction (UI)**: None (N) / Required (R)
- **Scope (S)**: Unchanged (U) / Changed (C)
- **Confidentiality Impact (C)**: None (N) / Low (L) / High (H)
- **Integrity Impact (I)**: None (N) / Low (L) / High (H)
- **Availability Impact (A)**: None (N) / Low (L) / High (H)

Use CVSS calculator: https://www.first.org/cvss/calculator/3.1

**Severity Ratings**:
- **Critical**: 9.0-10.0
- **High**: 7.0-8.9
- **Medium**: 4.0-6.9
- **Low**: 0.1-3.9
- **Info**: 0.0

#### 2.7 Calculate Impact × Likelihood

**Impact** (1-5):
1. Info - No real impact
2. Low - Minor inconvenience
3. Medium - Significant data exposure or service disruption
4. High - Major data breach or extended outage
5. Critical - Complete compromise or catastrophic data loss

**Likelihood** (1-5):
1. Very Low - Requires exceptional circumstances
2. Low - Difficult to exploit
3. Medium - Moderately difficult
4. High - Easy to exploit
5. Very High - Trivial, publicly known

**Risk Rating** = Impact × Likelihood:
- **Critical**: 20-25
- **High**: 12-19
- **Medium**: 6-11
- **Low**: 2-5
- **Info**: 1

#### 2.8 Develop Remediation

Provide:
- **Root Cause**: Why the vulnerability exists
- **Secure Pattern**: How to fix it properly
- **Code Example**: Before/after code showing the fix
- **Additional Recommendations**: Defense in depth measures
- **Testing Guidance**: How to verify the fix

### Step 3: Generate Finding Document

For each verified vulnerability, create a finding document using the template at `.claude/skills/secreview/templates/finding.md`.

Save as: `security-review/05-findings/FINDING-{ID}.md`

Where {ID} is zero-padded sequential number: 001, 002, 003, etc.

### Step 4: Track Findings

Maintain a findings summary list with:
- Finding ID
- Title
- Severity (CVSS + Risk Matrix)
- CWE
- Status (Verified / False Positive / Needs More Investigation)

## Output Format

### Individual Finding File

See `.claude/skills/secreview/templates/finding.md` for the full template.

Each finding file should include:
- Finding ID and title
- Executive summary
- Vulnerability details
- CWE mapping
- OWASP Top 10 mapping
- Affected code locations
- Proof-of-concept exploit
- CVSS v3.1 score and rating
- Impact × Likelihood score and rating
- Business impact assessment
- Remediation recommendations with code examples
- References

### Findings Summary File

Create `security-review/05-findings/README.md`:

```markdown
# Security Review - Vulnerability Findings Summary

**Application**: [Name]
**Phase**: Vulnerability Analysis
**Date**: [Date]
**Total Findings**: [count]

---

## Findings Overview

| Finding ID | Title | Severity (CVSS) | Severity (Matrix) | CWE | OWASP | Status |
|------------|-------|-----------------|-------------------|-----|-------|--------|
| FINDING-001 | SQL Injection in Search API | Critical (9.8) | Critical (25) | CWE-89 | A03:2021 | Verified |
| FINDING-002 | Path Traversal in File Upload | High (7.5) | High (16) | CWE-22 | A01:2021 | Verified |
| FINDING-003 | Stored XSS in Comments | High (8.2) | High (15) | CWE-79 | A03:2021 | Verified |
| ... | ... | ... | ... | ... | ... | ... |

---

## Severity Distribution

- **Critical**: [count] findings
- **High**: [count] findings
- **Medium**: [count] findings
- **Low**: [count] findings
- **Info**: [count] findings

---

## OWASP Top 10 Mapping

- **A01 - Broken Access Control**: [count] findings
- **A02 - Cryptographic Failures**: [count] findings
- **A03 - Injection**: [count] findings
- ...

---

## CWE Mapping

- **CWE-89 (SQL Injection)**: [count] findings
- **CWE-79 (XSS)**: [count] findings
- **CWE-22 (Path Traversal)**: [count] findings
- ...

---

## Quick Wins (Low Effort, High Impact)

[Findings that are easy to fix but have significant security impact]

1. **FINDING-XXX**: [Title] - [Why easy to fix]
2. ...

---

## Critical Remediations Required

[Findings that must be fixed before production deployment]

1. **FINDING-001**: SQL Injection in Search API
   - **Risk**: Complete database compromise
   - **Effort**: Low (switch to parameterized queries)
   - **Timeline**: Immediate

2. ...

---

## Long-Term Improvements

[Architectural or systemic issues requiring more effort]

1. **FINDING-XXX**: [Title]
   - **Risk**: [Description]
   - **Effort**: High (requires refactoring)
   - **Timeline**: [Timeframe]

2. ...

---

## False Positives / Non-Issues

[Analysis that didn't result in findings]

- **FLOW-003** (Profile Update): Initially suspected mass assignment, but proper validation confirmed
- ...

---

## Files Analyzed

[List all files examined during vulnerability analysis]
- `[filepath]` - [Vulnerabilities found or "No issues"]
- ...
```

## Analysis Instructions

1. **Be rigorous**: Only create findings for verified, exploitable vulnerabilities
2. **No false positives**: If you can't prove exploitability, mark as "Needs Investigation" or exclude
3. **Be specific**: Include exact file paths, line numbers, and code snippets
4. **Create real PoCs**: Exploits must be concrete and testable
5. **Calculate scores accurately**: Use CVSS calculator, don't guess
6. **Provide actionable remediation**: Code examples, not just generic advice
7. **Prioritize business impact**: Connect technical vulnerabilities to business consequences
8. **Use model assistance**: For complex vulnerability analysis, leverage the selected AI model
9. **Cross-reference**: Link findings to assets (Phase 2) and entry points (Phase 3)

## Tools to Use

- `Read`: Read source code for vulnerable flows
- `Grep`: Search for similar vulnerable patterns elsewhere
- **Model** (if needed): Use `{MODEL}` for complex analysis, but verify findings
- CVSS Calculator: https://www.first.org/cvss/calculator/3.1 (mental calculation or reference)

## Completion Criteria

The analysis is complete when:
- ✅ All high-priority flows from Phase 4 have been analyzed
- ✅ Each verified vulnerability has a finding document
- ✅ All findings have PoC exploits
- ✅ All findings have CVSS and risk matrix scores
- ✅ All findings have remediation guidance with code examples
- ✅ Findings summary `security-review/05-findings/README.md` is created
- ✅ False positives are documented

## After Completion

Present a summary to the user:
- Total findings: [count]
- Severity breakdown (Critical/High/Medium/Low)
- Top 3 most critical findings
- Quick wins (easy fixes with high impact)
- Recommended remediation priorities

Ask user: "Does this vulnerability analysis look complete? Should I proceed to Phase 6: Report Generation?"
