# Phase 6: Executive Report Generation

You are generating the final executive security review report. Your goal is to synthesize all findings from previous phases into a comprehensive, business-focused report that communicates security risks to both technical and non-technical stakeholders.

## Inputs
- **All Previous Phases**: Read all outputs from phases 1-5
  - `security-review/01-context.md`
  - `security-review/02-assets.md`
  - `security-review/03-attack-surface.md`
  - `security-review/04-data-flows.md`
  - `security-review/05-findings/*.md`
  - `security-review/05-findings/README.md`
- **Target Path**: `{TARGET_PATH}`

## Objectives

1. **Executive Summary**
   - High-level overview for C-level executives
   - Key security posture assessment
   - Critical risks in business terms
   - Recommended actions with timeline

2. **Risk Overview**
   - Aggregate risk metrics
   - Severity distribution
   - OWASP Top 10 coverage
   - Compliance impact

3. **Detailed Findings**
   - Organized by severity
   - Reference individual finding documents
   - Business impact for each finding
   - Remediation roadmap

4. **Security Posture Assessment**
   - Strengths (what's done well)
   - Weaknesses (systemic issues)
   - Comparison to industry standards
   - Maturity level assessment

5. **Remediation Roadmap**
   - Immediate actions (Critical/High)
   - Short-term improvements (Medium)
   - Long-term strategic changes (Architecture)
   - Resource requirements

6. **Compliance Considerations**
   - GDPR, HIPAA, PCI-DSS, SOC 2 (if applicable)
   - Regulatory risks
   - Audit readiness

## Report Structure

Use the template at `.claude/skills/secreview/templates/report.md` as the base structure.

## Analysis Process

### Step 1: Read All Previous Outputs
- Thoroughly review all phase outputs
- Extract key insights from each phase
- Identify patterns and systemic issues
- Note positive security practices

### Step 2: Aggregate Metrics
Calculate:
- Total findings by severity
- OWASP Top 10 coverage (how many categories have findings)
- CWE distribution
- Attack surface size metrics
- Asset protection coverage

### Step 3: Assess Business Impact
For each finding:
- Translate technical risk to business terms
- Quantify potential damage (data loss, downtime, fines)
- Identify regulatory/compliance implications
- Estimate reputational impact

### Step 4: Prioritize Remediation
Group findings into:
- **Immediate (0-2 weeks)**: Critical vulnerabilities requiring urgent fixes
- **Short-term (1-3 months)**: High/Medium vulnerabilities
- **Long-term (3-12 months)**: Architectural improvements, process changes
- **Continuous**: Ongoing security practices

Consider:
- Severity (CVSS + Risk Matrix)
- Exploitability (ease of attack)
- Asset value (what's at risk)
- Remediation effort (quick wins vs. major refactoring)

### Step 5: Identify Systemic Issues
Look for patterns:
- Lack of input validation across multiple endpoints
- Inconsistent authorization checks
- Missing security headers globally
- Outdated dependencies throughout
- Insufficient logging/monitoring

### Step 6: Highlight Positive Findings
Document what the application does well:
- Strong authentication mechanisms
- Proper use of parameterized queries
- Good secret management practices
- Effective rate limiting
- Comprehensive audit logging

### Step 7: Make Strategic Recommendations
Beyond individual findings:
- Security training for developers
- Secure SDLC improvements
- Security testing integration (SAST/DAST)
- Dependency management process
- Incident response planning

### Step 8: Generate Final Report
Use template, fill in all sections with insights from analysis.

## Output Format

Create `security-review/REPORT.md` using the template at `.claude/skills/secreview/templates/report.md`.

### Key Sections

1. **Cover Page**
   - Application name
   - Review date
   - Reviewer (Claude Security Review Skill)
   - Classification (Confidential)

2. **Executive Summary** (1-2 pages)
   - For C-level, non-technical stakeholders
   - Business-focused language
   - Key numbers (findings count, critical issues)
   - Recommended actions
   - Overall risk rating

3. **Methodology** (1 page)
   - 6-phase approach overview
   - Source-to-sink analysis explanation
   - Risk scoring methodology

4. **Application Overview** (1 page)
   - From Phase 1 context
   - Business purpose
   - Technology stack
   - Architecture summary

5. **Security Posture Assessment** (2-3 pages)
   - Overall security maturity
   - Strengths
   - Weaknesses
   - Comparison to industry standards
   - Security controls inventory

6. **Risk Overview** (2 pages)
   - Findings by severity (chart/table)
   - OWASP Top 10 mapping
   - CWE distribution
   - Attack surface metrics
   - Asset risk matrix

7. **Critical Findings** (3-5 pages)
   - Top 5-10 most severe findings
   - One paragraph per finding with:
     - What it is (technical)
     - Why it matters (business)
     - How to fix (high-level)
     - Reference to detailed finding doc

8. **All Findings Summary** (2-3 pages)
   - Table of all findings
   - Organized by severity
   - Quick reference to detailed docs

9. **Remediation Roadmap** (2-3 pages)
   - Phased approach
   - Timeline
   - Resource requirements
   - Dependencies between fixes

10. **Compliance Considerations** (1-2 pages)
    - GDPR, HIPAA, PCI-DSS, SOC 2
    - Regulatory risks
    - Compliance gaps

11. **Recommendations** (1-2 pages)
    - Process improvements
    - Training needs
    - Tooling recommendations
    - Strategic security initiatives

12. **Appendices**
    - Detailed findings (link to 05-findings/)
    - Methodology details
    - CVSS scoring reference
    - Glossary

## Report Writing Guidelines

### Executive Summary Writing
- **Audience**: CEO, CTO, CISO, Board members
- **Language**: Business-focused, avoid jargon
- **Length**: 1-2 pages maximum
- **Focus**: Risk, impact, actions needed
- **Tone**: Professional, factual, actionable

Example opening:
> "This security review of [Application Name] identified [X] security vulnerabilities across [Y] categories. Of these, [Z] are rated Critical or High severity and require immediate remediation. The most significant risks include [top 3 risks in business terms]. Without remediation, these vulnerabilities could result in [business impact: data breach, regulatory fines, service disruption, reputational damage]. We recommend prioritizing [immediate actions] within the next [timeframe]."

### Technical Findings Writing
- **Audience**: Developers, security engineers, DevOps
- **Language**: Technical, precise
- **Include**: Code references, PoCs, fix examples
- **Focus**: Root cause, remediation, verification

### Business Impact Writing
Translate technical risks:
- SQL Injection → "Complete database compromise, exposing all customer PII and payment data. Estimated impact: $X million in breach costs, regulatory fines (GDPR: up to 4% annual revenue), loss of customer trust."
- XSS → "Attackers can impersonate users, steal session tokens, and perform actions on their behalf. Could lead to account takeovers, fraudulent transactions, and reputational damage."
- Path Traversal → "Attackers can read/write arbitrary files on the server, potentially accessing secrets, modifying application code, or achieving full system compromise."

## Analysis Instructions

1. **Read thoroughly**: Review all phase outputs completely
2. **Synthesize**: Combine insights from all phases into coherent narrative
3. **Prioritize business impact**: Always connect technical findings to business consequences
4. **Be actionable**: Provide specific, implementable recommendations
5. **Use clear language**: Technical accuracy with business accessibility
6. **Include visuals**: Tables, charts (ASCII art if needed) for quick comprehension
7. **Cross-reference**: Link report sections to detailed phase outputs
8. **Be balanced**: Highlight both risks and strengths

## Tools to Use

- `Read`: Read all previous phase outputs and individual findings
- `Write`: Create the final report using the template

## Completion Criteria

The report is complete when:
- ✅ Executive summary is clear and business-focused
- ✅ All findings are included and organized by severity
- ✅ Remediation roadmap provides clear timeline and priorities
- ✅ Business impact is articulated for all critical/high findings
- ✅ Compliance considerations are addressed
- ✅ Report uses template structure from `templates/report.md`
- ✅ Report is saved as `security-review/REPORT.md`

## After Completion

Present to the user:
- Report location: `security-review/REPORT.md`
- Executive summary (show the first 2 paragraphs)
- Key statistics:
  - Total findings: [count]
  - Critical: [count], High: [count], Medium: [count], Low: [count]
  - Immediate actions required: [count]
  - Estimated remediation timeline: [timeline]
- Top 3 critical findings in one sentence each
- Recommended next steps

Provide a complete file tree of all outputs:
```
security-review/
├── 01-context.md
├── 02-assets.md
├── 03-attack-surface.md
├── 04-data-flows.md
├── 05-findings/
│   ├── README.md
│   ├── FINDING-001.md
│   ├── FINDING-002.md
│   └── ...
└── REPORT.md
```

Conclude with: "Security code review complete. All artifacts are saved in `security-review/`. You can now review the findings and begin remediation according to the roadmap in REPORT.md."
