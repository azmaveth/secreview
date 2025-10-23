# FINDING-{ID}: {Vulnerability Title}

**Finding ID**: FINDING-{ID}
**Title**: {Short descriptive title}
**Severity (CVSS v3.1)**: {Critical|High|Medium|Low|Info} ({Score})
**Severity (Risk Matrix)**: {Critical|High|Medium|Low|Info} ({Score})
**CWE**: [CWE-XXX]({CWE_URL})
**OWASP Top 10**: {Category}
**Status**: {Verified|False Positive|Needs Investigation}
**Date Found**: {YYYY-MM-DD}

---

## Executive Summary

{2-3 sentence summary for non-technical stakeholders explaining what the vulnerability is, why it matters, and what the business impact could be.}

**Business Impact**: {Critical|High|Medium|Low}
**Exploitability**: {Trivial|Easy|Moderate|Difficult|Very Difficult}
**Asset(s) at Risk**: {List high-value assets from Phase 2 that this vulnerability exposes}

---

## Vulnerability Details

### Description

{Detailed technical description of the vulnerability. Explain:
- What the vulnerability is
- Where it exists in the code
- How an attacker could exploit it
- What conditions must be met for exploitation
- What protections (if any) currently exist and why they're insufficient}

### Root Cause

{Explain why this vulnerability exists. Common root causes:
- Missing input validation
- Lack of output encoding
- Insufficient authorization checks
- Use of dangerous functions
- Insecure design pattern
- Legacy code without security review}

### Affected Components

| Component | File Location | Lines | Description |
|-----------|---------------|-------|-------------|
| {Component name} | `{filepath}` | {line_start}-{line_end} | {What this component does} |
| ... | ... | ... | ... |

### Attack Vector

**Attack Vector (CVSS)**: {Network|Adjacent|Local|Physical}
**Attack Complexity**: {Low|High}
**Privileges Required**: {None|Low|High}
**User Interaction**: {None|Required}

{Describe the attack path:
1. Step 1 - What the attacker does
2. Step 2 - How the system responds
3. Step 3 - Final exploitation}

---

## Proof of Concept

### Exploit Steps

{Step-by-step instructions to reproduce the vulnerability}

1. **Setup**: {Any prerequisites}
2. **Exploitation**: {Concrete steps}
3. **Verification**: {How to confirm success}

### HTTP Request (if applicable)

```http
POST /api/vulnerable-endpoint HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer {token}

{
  "parameter": "malicious_payload"
}
```

### Code Snippet

```{language}
# Example exploit code or attack payload
{exploit_code}
```

### Expected vs. Actual Behavior

**Expected** (Secure Behavior):
```
{What should happen if properly secured}
```

**Actual** (Vulnerable Behavior):
```
{What actually happens}
```

### Impact Demonstration

{Describe what an attacker achieves with this exploit:
- Data accessed/stolen
- Privileges gained
- System damage possible
- Lateral movement opportunities}

---

## Risk Assessment

### CVSS v3.1 Score

**Base Score**: {0.0-10.0} ({Critical|High|Medium|Low})

**Vector String**: `CVSS:3.1/AV:{N|A|L|P}/AC:{L|H}/PR:{N|L|H}/UI:{N|R}/S:{U|C}/C:{N|L|H}/I:{N|L|H}/A:{N|L|H}`

**Metrics**:
- **Attack Vector (AV)**: {Network|Adjacent|Local|Physical}
- **Attack Complexity (AC)**: {Low|High}
- **Privileges Required (PR)**: {None|Low|High}
- **User Interaction (UI)**: {None|Required}
- **Scope (S)**: {Unchanged|Changed}
- **Confidentiality Impact (C)**: {None|Low|High}
- **Integrity Impact (I)**: {None|Low|High}
- **Availability Impact (A)**: {None|Low|High}

**Justification**:
{Explain why each CVSS metric was scored as it was}

### Impact × Likelihood Matrix

**Impact**: {1-5} ({Informational|Low|Medium|High|Critical})
**Likelihood**: {1-5} ({Very Low|Low|Medium|High|Very High})
**Risk Score**: {Impact × Likelihood} = {1-25}
**Risk Rating**: {Info|Low|Medium|High|Critical}

**Impact Justification** ({1-5}):
{Explain the business impact if exploited:
- Data breach consequences
- Financial impact (fines, breach costs, revenue loss)
- Reputational damage
- Operational disruption
- Regulatory/compliance violations}

**Likelihood Justification** ({1-5}):
{Explain how likely exploitation is:
- Technical difficulty
- Prerequisites required
- Attacker motivation
- Public knowledge of technique
- Detection likelihood}

### Business Impact

{Translate technical risk to business terms}

**If Exploited**:
- **Confidentiality**: {What data could be exposed? PII, financial data, IP, credentials?}
- **Integrity**: {What could be modified or corrupted?}
- **Availability**: {What services could be disrupted?}
- **Financial**: {Estimated cost: breach notification, fines, lawsuits, customer compensation}
- **Regulatory**: {GDPR, HIPAA, PCI-DSS violations and penalties}
- **Reputational**: {Media coverage, customer trust loss, brand damage}

**Affected Users**: {How many users/customers could be impacted?}

---

## Remediation

### Recommended Fix

{High-level description of the proper solution}

### Code Changes Required

**Before** (Vulnerable):
```{language}
{Show the vulnerable code snippet from the application}
```

**After** (Secure):
```{language}
{Show the corrected code with security fixes applied}
```

### Explanation of Fix

{Explain what changed and why it's now secure:
- What security control was added
- How it prevents the attack
- Why this approach is recommended}

### Additional Security Measures

{Defense-in-depth recommendations beyond the primary fix:
- Input validation at multiple layers
- Output encoding
- Principle of least privilege
- Security monitoring/alerting
- Rate limiting
- WAF rules (if applicable)}

### Framework-Specific Guidance

{If using a specific framework, provide framework-idiomatic solutions}

**{Framework Name}**:
```{language}
{Framework-specific secure code example}
```

Reference: {Link to framework security docs}

### Testing the Fix

**Unit Tests**:
```{language}
{Example unit test to verify the fix}
```

**Manual Testing**:
1. {Step 1 - Attempt original exploit}
2. {Step 2 - Verify it's blocked}
3. {Step 3 - Test edge cases}

**Regression Testing**:
{Ensure the fix doesn't break legitimate functionality}

---

## Compliance Impact

### Regulatory Considerations

{If applicable, describe compliance implications}

- **GDPR**: {Does this expose EU personal data? Potential fines?}
- **HIPAA**: {Does this expose PHI? Breach notification required?}
- **PCI-DSS**: {Does this expose cardholder data? Compliance failure?}
- **SOC 2**: {Does this violate security controls? Audit implications?}
- **Other**: {Industry-specific regulations}

### Audit Trail

{Should this vulnerability be disclosed?}
- Internal security team: {Yes|No}
- Audit committee: {Yes|No}
- Regulators: {Yes|No|If exploited}
- Customers: {Yes|No|If exploited}

---

## References

### CWE

- **CWE-{XXX}**: {CWE Name}
  - URL: https://cwe.mitre.org/data/definitions/{XXX}.html
  - Description: {Brief CWE description}

### OWASP

- **OWASP Top 10 {Year}**: {Category}
  - URL: {OWASP URL}

### Related Vulnerabilities

{If similar vulnerabilities exist elsewhere in the codebase, list them here}
- {Location 1}: {Description}
- {Location 2}: {Description}

### External References

- {Relevant security articles, blog posts, CVEs}
- {NIST, SANS, or other security guidance}
- {Language/framework-specific security resources}

---

## Timeline

- **Discovered**: {YYYY-MM-DD}
- **Verified**: {YYYY-MM-DD}
- **Reported**: {YYYY-MM-DD}
- **Fix Target**: {YYYY-MM-DD}
- **Fixed**: {YYYY-MM-DD or "Not yet fixed"}
- **Verified Fixed**: {YYYY-MM-DD or "Not yet verified"}

---

## Notes

{Any additional context, edge cases, or observations}

---

**Reviewer**: Claude Security Analysis Skill
**Review Date**: {YYYY-MM-DD}
**Model Used**: {Model name}
