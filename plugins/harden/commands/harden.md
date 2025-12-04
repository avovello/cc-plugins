---
description: Security hardening with vulnerability scanning, configuration auditing, and security improvements
---

# Harden Command

**Purpose**: Security hardening through systematic vulnerability scanning, configuration auditing, and comprehensive security improvements

## Overview

The harden command provides holistic security improvement beyond finding specific vulnerabilities. Research shows that **60% of breaches exploit known vulnerabilities** that could have been prevented through proper hardening.

**Key Distinction**: Harden provides comprehensive security improvement, while Review finds specific issues in code.

## Workflow

### Step 1: Validate Target

1. **Check target exists**
   - If specific component provided: verify it exists
   - If security goal provided: identify scope
   - If no target: offer full application security hardening

2. **Determine hardening scope**
   - Application security
   - Authentication/authorization
   - Infrastructure security
   - Data protection
   - API security
   - Compliance requirements

3. **Identify technology stack**
   - Languages and frameworks
   - Database systems
   - Web servers
   - Cloud infrastructure
   - Third-party services

### Step 2: Scan for Vulnerabilities

Run **vulnerability-scanner** agent to:
- Scan dependencies for known CVEs
- Check OWASP Top 10 vulnerabilities
- Identify insecure configurations
- Find exposed secrets
- Detect security misconfigurations
- Scan for common vulnerabilities (SQL injection, XSS, etc.)

**Output**:
```
harden-analysis/
├── VULNERABILITIES.md         # Identified vulnerabilities
├── CVE_REPORT.md             # Known CVEs in dependencies
├── SECURITY_MISCONFIG.md     # Configuration issues
└── EXPOSED_SECRETS.md        # Potential secrets in code
```

### Step 3: Audit Security Configuration

Run **security-config-auditor** agent to:
- Review authentication configuration
- Check authorization setup
- Audit CORS policies
- Review HTTPS/TLS configuration
- Check CSP headers
- Audit logging and monitoring
- Review error handling

**Output**: Configuration security assessment

### Step 4: Prioritize Issues

Prioritize vulnerabilities by:
- **Severity**: Critical, High, Medium, Low
- **Exploitability**: How easy to exploit
- **Impact**: Data breach, service disruption, etc.
- **CVSS Score**: Industry-standard scoring

**Output**: Prioritized security roadmap

### Step 5: Create Hardening Plan

Design comprehensive hardening approach:
- Authentication hardening
- Secrets management
- Security headers
- Input validation
- Dependency updates
- Configuration fixes

**Output**: `HARDENING_PLAN.md` with step-by-step approach

### Step 6: Launch Specialized Hardening Agents

Based on findings, launch appropriate agents:

**Authentication/Authorization**:
- **auth-hardener** - Strengthen authentication and authorization

**Secrets Management**:
- **secrets-manager** - Secure API keys, passwords, tokens

**Infrastructure Security**:
- **security-header-configurator** - Add security headers (CSP, HSTS, etc.)

**Penetration Testing**:
- **penetration-tester** - Test security controls

**Compliance**:
- **compliance-checker** - Verify compliance standards (GDPR, SOC2, etc.)

### Step 7: Apply Hardening Incrementally

For each security improvement:

1. **Apply Fix**
   - Make security improvement
   - Update configuration

2. **Test Functionality**
   - Ensure application still works
   - No breaking changes

3. **Verify Security**
   - Confirm vulnerability fixed
   - No new vulnerabilities introduced

4. **If Successful**: ✅
   - Document fix
   - Commit change
   - Move to next item

### Step 8: Penetration Test

Run **penetration-tester** agent to:
- Test authentication bypass attempts
- Try SQL injection
- Test XSS vulnerabilities
- Attempt CSRF attacks
- Test authorization flaws
- Try common exploits

**Success Criteria**:
- ✅ No critical vulnerabilities
- ✅ All high-severity issues fixed
- ✅ Security controls working
- ✅ Penetration tests pass

### Step 9: Verify Compliance

Run **compliance-checker** agent to:
- Verify OWASP Top 10 coverage
- Check compliance standards (GDPR, HIPAA, PCI-DSS, SOC2)
- Validate security controls
- Document compliance status

### Step 10: Document Security Improvements

Generate documentation:
```
harden-output/
├── HARDENING_SUMMARY.md      # What was hardened
├── VULNERABILITIES_FIXED.md  # Issues resolved
├── SECURITY_CONTROLS.md      # New security measures
├── COMPLIANCE_STATUS.md      # Compliance verification
└── SECURITY_CHECKLIST.md     # Ongoing security tasks
```

## Configuration

Optional `.claude/harden-config.yaml`:

```yaml
harden:
  scope:
    scan_dependencies: true           # Scan for CVEs
    scan_code: true                   # Scan source code
    audit_configuration: true         # Audit config files
    test_security_controls: true      # Penetration testing

  severity_thresholds:
    fix_critical: true                # Auto-fix critical issues
    fix_high: true                    # Auto-fix high severity
    report_medium: true               # Report medium severity
    report_low: false                 # Ignore low severity

  compliance:
    standards:                        # Compliance requirements
      - owasp-top-10
      - gdpr
      - soc2

  secrets:
    scan_patterns:                    # Patterns for secret detection
      - api_key
      - password
      - secret
      - token
      - private_key
```

## Usage Examples

### Example 1: Full Application Hardening

```bash
/harden "comprehensive security hardening"

# Output:
# ✅ Scanned 245 dependencies (found 8 CVEs)
# ✅ Scanned source code (found 12 vulnerabilities)
# ✅ Audited configuration (found 15 issues)
#
# Critical Issues (3):
#   1. SQL injection vulnerability in search endpoint
#   2. Hardcoded API key in config
#   3. Missing authentication on admin endpoint
#
# ✅ Step 1/20: Fixed SQL injection with parameterized queries
# ✅ Step 2/20: Moved API key to environment variable
# ✅ Step 3/20: Added authentication middleware to admin routes
# ...
#
# Security hardening complete:
# - 3 critical vulnerabilities fixed
# - 5 high-severity issues resolved
# - 8 dependencies updated
# - 15 configuration improvements applied
# ✅ All 156 tests pass
```

### Example 2: Strengthen Authentication

```bash
/harden "strengthen authentication and authorization"

# Output:
# ✅ Audited authentication system
# ✅ Found 6 issues:
#    1. Weak password requirements (min 6 chars)
#    2. No rate limiting on login
#    3. JWT tokens never expire
#    4. No MFA support
#    5. Session fixation vulnerability
#    6. Insufficient authorization checks
#
# ✅ Applied hardening:
#    1. Increased password requirements (12+ chars, complexity)
#    2. Added rate limiting (5 attempts per 15 min)
#    3. Set JWT expiration (1 hour access, 7 days refresh)
#    4. Implemented MFA with TOTP
#    5. Regenerate session ID on login
#    6. Added role-based authorization checks
#
# Authentication hardened ✅
# Penetration tests pass ✅
```

## Quality Gates

Before completing hardening:

- ✅ All critical vulnerabilities fixed
- ✅ All high-severity issues addressed
- ✅ No secrets in code
- ✅ Security headers configured
- ✅ Penetration tests pass
- ✅ All tests pass

## Safety Features

1. **Non-Breaking**: Security fixes preserve functionality
2. **Tested**: All changes tested for correctness
3. **Incremental**: Small, focused security improvements
4. **Validated**: Penetration testing confirms fixes
5. **Documented**: All changes documented

## Output Files

```
harden-output/
├── VULNERABILITY_SCAN.md     # Initial scan results
├── HARDENING_PLAN.md         # Step-by-step plan
├── HARDENING_SUMMARY.md      # What was hardened
├── VULNERABILITIES_FIXED.md  # Issues resolved
├── SECURITY_CONTROLS.md      # Security measures added
├── PENETRATION_TEST.md       # Security test results
├── COMPLIANCE_STATUS.md      # Compliance verification
└── SECURITY_CHECKLIST.md     # Ongoing security tasks
```

## Integration with Other Plugins

- **After Review**: `/review` finds issues → `/harden` fixes them comprehensively
- **Before Deploy**: Harden before production deployment
- **With Audit**: `/audit` identifies debt, `/harden` improves security
- **Ongoing**: Regular security hardening (monthly/quarterly)

## Best Practices

1. **Defense in Depth**: Multiple layers of security
2. **Least Privilege**: Minimal permissions required
3. **Fail Secure**: Default to secure state on errors
4. **Keep Updated**: Regular dependency updates
5. **Security by Design**: Build security in from the start
6. **Test Security**: Regular penetration testing
