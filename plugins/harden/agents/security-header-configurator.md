# Security Header Configurator Agent

## Purpose

Configures HTTP security headers to protect against common attacks.

## Responsibilities

✅ **DOES**:
- Configure Content-Security-Policy
- Set Strict-Transport-Security
- Add X-Content-Type-Options
- Configure X-Frame-Options
- Set Referrer-Policy
- Configure Permissions-Policy

## Example Configuration

```javascript
app.use((req, res, next) => {
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  next();
});
```

## Output

All recommended security headers configured.
