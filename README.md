# Security Code Review Skill (`secreview`)

A comprehensive security-focused code review skill that uses source-to-sink methodology to identify vulnerabilities, assess risk, and provide actionable remediation guidance.

## Overview

The `secreview` skill performs a systematic 6-phase security analysis:

1. **Business Context Discovery** - Understand the application's purpose and architecture
2. **Asset Identification** - Identify valuable targets from an attacker's perspective
3. **Attack Surface Enumeration** - Map all external interaction points
4. **Data Flow Analysis** - Trace data from sources to sinks, prioritize by risk
5. **Vulnerability Analysis** - Deep dive on high-risk flows with PoC exploits
6. **Report Generation** - Executive summary with remediation roadmap

## Features

✅ **Multi-language support** - Automatically detects and adapts to any programming language/framework
✅ **OWASP Top 10 coverage** - Checks for all major vulnerability categories
✅ **Dual risk scoring** - Both CVSS v3.1 (technical) and Impact×Likelihood (business) scores
✅ **Interactive checkpoints** - User review and approval between phases
✅ **Exploitable PoCs** - Concrete proof-of-concept attacks for verified findings
✅ **Remediation-first** - Every finding includes fix code examples
✅ **Compliance mapping** - GDPR, HIPAA, PCI-DSS, SOC 2 considerations
✅ **Reusable artifacts** - All outputs saved as markdown for incremental analysis

## Installation

### Prerequisites
- [Claude Code](https://claude.com/claude-code) installed
- Git

### Install the Skill

Clone this repository into your Claude skills directory:

```bash
# For project-specific installation
cd /path/to/your/project
git clone https://github.com/azmaveth/secreview.git .claude/skills/secreview

# For global installation (available in all projects)
git clone https://github.com/azmaveth/secreview.git ~/.claude/skills/secreview
```

After installation, the `/secreview` command will be available in Claude Code.

## Usage

### Basic Invocation

```bash
# Analyze current directory (model will be prompted)
/secreview .

# Analyze specific directory with model selection
/secreview ./my-app --model gemini-2.5-pro

# Analyze with GPT-5 Pro
/secreview /path/to/app --model gpt-5-pro
```

### Recommended Models

- **gemini-2.5-pro**: Best for large codebases (1M token context window)
- **gpt-5-pro**: Excellent balanced analysis (400K context window)
- **gpt-5-codex**: Specialized for code analysis (400K context window)

### Expected Timeline

- **Small apps** (<10K LOC): 1-1.5 hours
- **Medium apps** (10-50K LOC): 1.5-2.5 hours
- **Large apps** (>50K LOC): 2.5-4 hours

## Output Structure

All outputs are saved to `security-review/` directory:

```
security-review/
├── 01-context.md           # Business purpose, tech stack, architecture
├── 02-assets.md            # High-value assets, sensitive data inventory
├── 03-attack-surface.md    # Entry points, trust boundaries
├── 04-data-flows.md        # Source-to-sink analysis, prioritization
├── 05-findings/            # Individual vulnerability reports
│   ├── README.md           # Findings summary
│   ├── FINDING-001.md      # Detailed finding with PoC, CVSS, remediation
│   ├── FINDING-002.md
│   └── ...
└── REPORT.md               # Executive summary & remediation roadmap
```

## Vulnerability Categories Checked

### OWASP Top 10 (2021)
- A01: Broken Access Control
- A02: Cryptographic Failures
- A03: Injection (SQL, Command, XSS, etc.)
- A04: Insecure Design
- A05: Security Misconfiguration
- A06: Vulnerable and Outdated Components
- A07: Identification and Authentication Failures
- A08: Software and Data Integrity Failures
- A09: Security Logging and Monitoring Failures
- A10: Server-Side Request Forgery (SSRF)

### Language-Specific Checks

**JavaScript/TypeScript**:
- Prototype pollution
- eval() and code injection
- Regex DoS
- JWT vulnerabilities
- NPM dependency vulnerabilities

**Python**:
- Pickle deserialization
- OS command injection
- Template injection (Jinja2, Django)
- SQL injection in raw queries

**Elixir/Phoenix**:
- Ecto SQL injection
- Plug CSRF bypass
- Phoenix LiveView XSS
- Insecure channel authentication
- Atom exhaustion DoS

**Ruby/Rails**:
- Mass assignment
- SQL injection
- YAML deserialization RCE
- ERB template injection

**Java/Kotlin**:
- Insecure deserialization
- XXE (XML External Entity)
- Spring Security misconfigurations

**Go**:
- SQL injection via string concatenation
- Command injection
- Path traversal
- Race conditions

**Rust**:
- Unsafe block vulnerabilities
- FFI boundary issues
- Integer overflow in unsafe contexts

## Risk Scoring

### CVSS v3.1 (Technical Severity)

Calculates standardized vulnerability severity based on:
- Attack vector, complexity, privileges required
- User interaction needed
- Impact on confidentiality, integrity, availability

**Severity Ratings**:
- Critical: 9.0-10.0
- High: 7.0-8.9
- Medium: 4.0-6.9
- Low: 0.1-3.9
- Info: 0.0

### Impact × Likelihood Matrix (Business Risk)

Assesses business risk with simplified scoring:
- **Impact** (1-5): Business consequence if exploited
- **Likelihood** (1-5): Probability of exploitation
- **Risk Score**: Impact × Likelihood (1-25)

**Risk Ratings**:
- Critical: 20-25
- High: 12-19
- Medium: 6-11
- Low: 2-5
- Info: 1

## Example Output

See `examples/sample-output.md` for a complete example of skill output for a hypothetical e-commerce application.

**Sample Finding** (SQL Injection):
- Finding ID, title, severity (both CVSS and risk matrix)
- CWE mapping, OWASP category
- Proof-of-concept HTTP request demonstrating exploit
- Code location with before/after remediation examples
- Business impact assessment
- Compliance implications (GDPR, PCI-DSS, etc.)

**Sample Report** (Executive Summary):
- Overall security posture assessment
- Severity distribution chart
- Top 3 critical risks in business terms
- Remediation roadmap with timeline
- Compliance considerations

## Interactive Workflow

The skill uses checkpoints between phases for user review:

```
Phase 1: Business Context Discovery
↓
[Checkpoint] Present summary, ask: "Proceed to Phase 2?"
↓
Phase 2: Asset Identification
↓
[Checkpoint] Present assets, ask: "Add assets or proceed to Phase 3?"
↓
Phase 3: Attack Surface Enumeration
↓
[Checkpoint] Present attack surface, ask: "Proceed to Phase 4?"
↓
Phase 4: Data Flow Analysis
↓
[Checkpoint] Present prioritized flows, ask: "Proceed to Phase 5?"
↓
Phase 5: Vulnerability Analysis
↓
[Checkpoint] Present findings summary, ask: "Proceed to Phase 6?"
↓
Phase 6: Report Generation
↓
[Complete] Present final report location and statistics
```

This allows you to:
- Verify accuracy at each stage
- Add additional context or assets
- Adjust priorities before deep analysis
- Stop early if needed

## Customization

### Modify Security Patterns

To add new vulnerability checks:
1. Edit `prompts/05-findings.md`
2. Add new CWE mappings and detection logic
3. Update language-specific patterns in `skill.md`

### Adjust Risk Scoring

To customize CVSS or risk matrix calculations:
1. Edit scoring guidelines in `prompts/05-findings.md`
2. Update templates in `templates/finding.md`

### Add Language Support

To support new languages:
1. Add language detection patterns to `prompts/01-discovery.md`
2. Add language-specific vulnerability patterns to `prompts/05-findings.md`
3. Update language list in `skill.md`

## Best Practices

### Before Running
- Ensure you have read access to the entire codebase
- Review and understand what the application does
- Have documentation ready (README, architecture diagrams)

### During Analysis
- Carefully review each phase summary before proceeding
- Add any additional high-value assets you're aware of
- Verify the attack surface enumeration is complete
- Challenge the vulnerability findings (no false positives)

### After Completion
- Review the executive report first for business context
- Share detailed findings with development team
- Prioritize remediation according to the roadmap
- Schedule follow-up review after critical fixes

## Limitations

### What This Skill Does NOT Do
- **Runtime testing**: No dynamic analysis or active exploitation
- **Dependency CVE scanning**: Use dedicated SCA tools (Snyk, Dependabot)
- **Infrastructure review**: Focuses on application code, not cloud configs
- **Compliance audit**: Provides guidance but not formal compliance certification
- **Automatic remediation**: Suggests fixes but doesn't apply them

### Known Edge Cases
- Very large monorepos (>500K LOC) may require multiple reviews by subsystem
- Heavily obfuscated code may reduce analysis quality
- Proprietary frameworks may require manual security pattern research
- Microservices architectures need per-service analysis

## Troubleshooting

### "Model context limit exceeded"
- Use a model with larger context (gemini-2.5-pro has 1M tokens)
- Reduce scope to a specific subdirectory
- Run phases independently and reuse intermediate artifacts

### "No entry points found"
- Ensure correct path to application root
- Check if framework is supported (check `skill.md` for patterns)
- Manually review `03-attack-surface.md` and add missing endpoints

### "False positives in findings"
- Review the PoC - can you actually exploit it?
- Check validation logic - is there protection the analysis missed?
- Mark as false positive in findings summary and exclude from report

## Contributing

To improve this skill:
1. Add new language-specific patterns to `skill.md`
2. Update vulnerability detection logic in phase prompts
3. Add real-world examples to `examples/`
4. Improve templates with better formatting/clarity

## References

- [OWASP Top 10 (2021)](https://owasp.org/www-project-top-ten/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [CVSS v3.1 Calculator](https://www.first.org/cvss/calculator/3.1)
- [OWASP Code Review Guide](https://owasp.org/www-project-code-review-guide/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

**Created by**: Claude (Anthropic)
**Version**: 1.0
**Last Updated**: 2025-10-21
