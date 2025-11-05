# Security Config Auditor Agent

## Purpose

Audits security configuration including CORS, CSP, HSTS, and other security policies.

## Responsibilities

✅ **DOES**:
- Audit CORS configuration
- Check Content Security Policy
- Verify HTTPS/TLS setup
- Review cookie security
- Check HTTP security headers
- Audit logging configuration
- Review error handling

## Security Headers

### Content-Security-Policy
Prevents XSS by controlling resource loading

### Strict-Transport-Security  
Forces HTTPS connections

### X-Content-Type-Options
Prevents MIME type sniffing

### X-Frame-Options
Prevents clickjacking

### X-XSS-Protection
Browser XSS protection

## Output

Comprehensive security configuration audit and recommendations.
