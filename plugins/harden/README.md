# Harden Plugin

Security hardening through systematic vulnerability scanning, configuration auditing, and comprehensive security improvements.

## Overview

The Harden plugin provides holistic security improvement beyond finding specific vulnerabilities. Research shows that **60% of breaches exploit known vulnerabilities** that could have been prevented through proper hardening.

**Key Distinction**:
- **Harden** provides comprehensive security improvement (this plugin)
- **Review** finds specific issues in code review

## Use Cases

- **Full Application Hardening**: Comprehensive security improvements
- **Authentication Strengthening**: MFA, rate limiting, secure sessions
- **Secrets Management**: Remove hardcoded secrets, use secret managers
- **Dependency Security**: Update packages with known CVEs
- **Compliance**: Meet OWASP, GDPR, SOC2, PCI-DSS requirements
- **Penetration Testing**: Validate security controls

## Usage

```bash
/harden "comprehensive security hardening"
/harden "strengthen authentication and authorization"
/harden "fix vulnerabilities from security scan"
/harden "ensure GDPR compliance"
/harden                                    # Interactive mode
```

## Specialized Agents (7)

### 1. **vulnerability-scanner**
Scans for CVEs, OWASP Top 10, secrets, and misconfigurations

### 2. **security-config-auditor**
Audits CORS, CSP, HSTS, and security policies

### 3. **auth-hardener**
Strengthens authentication with MFA, rate limiting, secure sessions

### 4. **secrets-manager**
Finds and secures API keys, passwords, tokens

### 5. **security-header-configurator**
Adds CSP, HSTS, X-Frame-Options, and security headers

### 6. **penetration-tester**
Tests security controls through ethical hacking

### 7. **compliance-checker**
Verifies OWASP, GDPR, SOC2, PCI-DSS compliance

## Example Output

```
✅ Scanned 245 dependencies (found 8 CVEs)
✅ Scanned source code (found 12 vulnerabilities)
✅ Audited configuration (found 15 issues)

Critical Issues (3):
  1. SQL injection vulnerability in search endpoint
  2. Hardcoded API key in config
  3. Missing authentication on admin endpoint

✅ Fixed all 3 critical vulnerabilities
✅ Resolved 8 high-severity issues
✅ Updated 8 dependencies
✅ Applied 15 configuration improvements
✅ All 156 tests pass
```

## Output

```
harden-output/
├── VULNERABILITY_SCAN.md
├── HARDENING_PLAN.md
├── HARDENING_SUMMARY.md
├── VULNERABILITIES_FIXED.md
├── SECURITY_CONTROLS.MD
├── PENETRATION_TEST.md
├── COMPLIANCE_STATUS.md
└── SECURITY_CHECKLIST.md
```

## Related Plugins

- **QA Plugin**: Run regression tests after security fixes (`/qa`, `/e2e`, `/unit`, `/integration`)
  - Ensure security hardening didn't break functionality
  - E2E tests for auth flow changes
  - Integration tests for API security changes

**Priority**: P2 - HIGH
**Why**: Security is critical for protecting user data and maintaining trust
