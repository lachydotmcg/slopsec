# slopsec audit checklist

Walk every box. Mark `[x]` handled, `[!]` finding, `[-]` N/A. Don't skip a
category because the app "looks fine" in the browser.

## Recon (do this first)
- [ ] Stack identified: frontend / backend / database / auth provider / host
- [ ] Where do secrets live? (env, secrets manager, ...)
- [ ] Is the repo public or private? Any history of committed secrets?
- [ ] Grep the **built/deployed** frontend bundle for keys, tokens, URLs

## Secrets & exposure
- [ ] No DB credentials in client code / public repo (1)
- [ ] `.env` not served at any URL, not committed (2)
- [ ] No hardcoded API keys in source (3)
- [ ] Debug mode/pages off in prod (10)
- [ ] CI/build logs don't print secrets (11)
- [ ] Errors return generic messages, no stack traces to client (12)
- [ ] No secrets in git history; repo visibility correct (13)
- [ ] No secrets in the frontend JS bundle (14)
- [ ] Source maps not served in prod (36)

## AuthN & AuthZ
- [ ] All protected endpoints require authentication (4)
- [ ] Authorization checked server-side on every sensitive action (5)
- [ ] Cannot access another user's data by changing an ID (6) — **test it**
- [ ] Admin routes enforce admin role server-side (9)
- [ ] No IDOR: every object access is ownership-checked (33)
- [ ] Identity/role come from the session, never the request body (34)

## Database & storage
- [ ] RLS / Firestore rules / bucket policies deny by default (7)
- [ ] No public buckets; signed URLs for access (8)
- [ ] App DB user has least privilege (41)
- [ ] Tenant isolation holds — test cross-tenant access (49)

## Injection & input
- [ ] Server-side input validation on all inputs (16)
- [ ] Parameterized queries — no SQL injection (17)
- [ ] No NoSQL operator injection (18)
- [ ] Output escaped; CSP set — no XSS (19)
- [ ] CSRF protection on state-changing requests (20)
- [ ] File uploads validated, stored safely, not executable (21)
- [ ] No path traversal in file handling (22)
- [ ] SSRF protection: destination allowlist, block internal IPs/metadata (23)

## Sessions, tokens & cookies
- [ ] Password reset tokens random, single-use, expiring (24)
- [ ] Sessions rotate on login, expire when idle (25)
- [ ] JWT secret strong & private; `alg:none` rejected (26)
- [ ] CORS allowlists specific origins; no `*` + credentials (27)
- [ ] Cookies set HttpOnly, Secure, SameSite (47)

## Frontend trust boundary
- [ ] No security check exists only client-side (15)
- [ ] Payment/subscription entitlement verified server-side (32)

## Rate limiting & exposure surface
- [ ] Rate limits on login, signup, APIs, AI endpoints (28)
- [ ] Staging/test envs not publicly exposed (29)
- [ ] No default credentials left unchanged (30)
- [ ] Webhook signatures verified (31)
- [ ] No internal dashboards exposed to the internet (45)

## AI-specific
- [ ] Prompt injection mitigated; no secrets in prompts; output validated (39)
- [ ] AI tool actions run under user authz, not ambient admin (40)

## Ops, logging & dependencies
- [ ] Logs redact tokens/PII/passwords (35)
- [ ] No known-vuln dependencies (37)
- [ ] Dependencies reasonably current (38)
- [ ] Audit logs for security-relevant events (42)
- [ ] Monitoring/alerting in place (43)
- [ ] Tested backup & restore plan (44)
- [ ] Security headers set (HSTS, CSP, etc.) (46)
- [ ] Sensitive data encrypted in transit & at rest; passwords hashed (48)
- [ ] Generated code reviewed before shipping (50)
