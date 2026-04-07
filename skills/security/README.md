# Skill: Security

## Purpose
Catch common security issues before they reach production.

## Checklist (review before every PR)

### Input & Output
- [ ] All user input is validated at system boundaries
- [ ] Output is properly escaped/encoded (XSS prevention)
- [ ] SQL queries use parameterized statements (no string concatenation)

### Authentication & Authorization
- [ ] Auth checks happen server-side, never client-side only
- [ ] Principle of least privilege applied to roles and permissions
- [ ] Sensitive tokens/keys are not logged or exposed in responses

### Dependencies
- [ ] No known vulnerabilities in added packages (`npm audit` / `pip-audit`)
- [ ] Third-party scripts load from trusted sources only

### Data
- [ ] PII is not stored beyond what is necessary
- [ ] Secrets are stored in environment variables or a secrets manager, never in code

## Common Anti-patterns to Avoid
- Trusting client-supplied IDs without authorization check
- Returning raw error stack traces to clients
- Hardcoded credentials in source code
