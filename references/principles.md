# The 50 principles

Each item: **what it is** → **what to look for** → **how to fix**. Grouped into
the 9 audit categories. Numbers match the original list.

---

## 1. Secrets & exposure

Anything secret that the public, the client, or a log can reach is already
compromised. Assume bots scrape these within minutes of launch.

**1. Exposed database credentials** — DB connection strings in client code,
public repos, or env files served by the web server.
→ Look for: connection strings in frontend, committed `.env`, configs in the
served bundle. → Fix: secrets only in server-side env / a secrets manager;
rotate anything that ever touched a repo or browser.

**2. Public `.env` files** — `.env` reachable at a URL, or committed to git.
→ Look for: `curl https://app/.env`, `.env` in git history. → Fix: never serve
`.env`; add to `.gitignore`; rotate exposed values; block dotfiles at the proxy.

**3. Hardcoded API keys** — keys pasted directly into source.
→ Look for: string literals like `sk-`, `AKIA`, `AIza` in code. → Fix: move to
env vars; rotate; for third-party calls that need a secret, proxy through the
backend so the key never reaches the client.

**10. Debug pages exposed in production** — `/debug`, `/test`, framework debug
toolbars live in prod.
→ Look for: stack-trace debug pages, admin debug routes, `DEBUG=true`. → Fix:
disable debug mode in prod; gate or remove debug endpoints.

**11. Build logs leaking secrets** — CI/CD logs printing env vars or tokens.
→ Look for: `echo $SECRET`, secrets in public CI output. → Fix: mask secrets in
CI; never print env; restrict log visibility.

**12. Verbose error messages leaking stack traces** — raw exceptions, SQL
errors, file paths returned to the client.
→ Look for: 500s with stack traces, DB error strings in responses. → Fix: catch
errors, return generic messages, log details server-side only.

**13. Leaked GitHub repos or commit history** — secrets removed from HEAD but
still in history; private code in a public repo.
→ Look for: `git log -p` for keys, public repos that should be private. → Fix:
rotate every secret that was ever committed (history rewrite is not enough on
its own); make repos private; scan with a secret scanner.

**14. Secrets included in frontend JavaScript** — secrets bundled into the SPA.
→ Look for: grep the built JS for keys/tokens. → Fix: anything in the bundle is
public; move to backend; use only publishable/anon keys client-side.

**36. Source maps exposed in production** — `.map` files reveal original source.
→ Look for: `.js.map` served in prod. → Fix: disable source map upload to the
public bundle, or restrict access.

---

## 2. Authentication & authorization

AuthN = who you are. AuthZ = what you may do. Vibe-coded apps routinely ship the
first and forget the second.

**4. Weak or missing authentication** — endpoints with no auth, guessable
passwords, no MFA option.
→ Look for: API routes returning data with no token, weak password policy.
→ Fix: require auth on protected routes; strong password rules; offer MFA; use
a vetted auth provider.

**5. No authorization checks** — authenticated users can do anything.
→ Look for: any logged-in user hitting admin/other-user actions. → Fix: enforce
role/ownership checks on every sensitive operation, server-side.

**6. Users able to access other users' data** — change an ID in a request, get
someone else's record.
→ Look for: `/api/orders/123` works for any logged-in user. → Fix: scope every
query to the authenticated user; verify ownership before returning data.

**9. Admin routes left unprotected** — `/admin` reachable without admin role.
→ Look for: admin pages/APIs loading for normal users. → Fix: enforce role
checks server-side on all admin routes; don't rely on hiding the link.

**33. Insecure direct object references (IDOR)** — direct access to objects by
ID without an ownership check.
→ Look for: sequential/guessable IDs in URLs returning data cross-account.
→ Fix: authorize every object access; consider non-guessable IDs as defense in
depth (not a substitute for authz).

**34. API endpoints that trust user-controlled IDs or roles** — client sends
`userId` or `role` and the server believes it.
→ Look for: `role` or `userId` read from request body to grant access. → Fix:
derive identity and role from the server-side session, never from the request.

---

## 3. Database & storage

The data layer must enforce its own rules. Don't depend on the app being the
only thing that talks to the database.

**7. Open database read/write permissions** — DB rules allow anyone to
read/write.
→ Look for: Supabase RLS disabled, Firestore `allow read, write: if true`.
→ Fix: enable RLS / deny-by-default rules; scope policies to the owner.

**8. Misconfigured Firebase / Supabase / S3 buckets** — public buckets, world-
readable storage.
→ Look for: public S3 buckets, open Firebase Storage rules. → Fix: private by
default; signed URLs for access; least-privilege bucket policies.

**41. Excessive database permissions for the app user** — app connects as a
superuser / can drop tables.
→ Look for: app DB role with admin rights. → Fix: least-privilege DB role —
only the tables and operations the app needs.

**49. Poor tenant isolation in multi-user apps** — one tenant can read another
tenant's data.
→ Look for: queries not filtered by tenant, shared keys across tenants. → Fix:
enforce tenant scoping at the data layer (RLS with tenant_id); test cross-tenant
access explicitly.

---

## 4. Injection & input

Never trust input. Validate on the server, parameterize queries, escape output.

**16. Missing input validation** — server accepts whatever the client sends.
→ Fix: validate type, length, format, and range server-side (e.g. a schema
validator like zod/pydantic). Reject by default.

**17. SQL injection** — user input concatenated into SQL.
→ Look for: string-built queries. → Fix: parameterized queries / prepared
statements / an ORM; never concatenate input.

**18. NoSQL injection** — operator injection in Mongo-style queries.
→ Look for: passing raw request objects into queries (`{$gt:''}` tricks). → Fix:
validate/cast inputs; don't pass raw objects as query filters.

**19. Cross-site scripting (XSS)** — untrusted data rendered as HTML/JS.
→ Look for: `dangerouslySetInnerHTML`, `innerHTML`, unescaped output. → Fix:
escape output; avoid raw HTML injection; set a Content-Security-Policy.

**20. Cross-site request forgery (CSRF)** — state-changing requests forgeable
from another site.
→ Fix: anti-CSRF tokens or SameSite cookies; don't perform mutations on GET.

**21. Insecure file uploads** — accepting arbitrary files, trusting filename or
content-type.
→ Fix: validate type/size, store outside webroot, randomize names, scan, never
execute uploads; serve from a separate domain.

**22. Path traversal bugs** — `../` in a path reaches arbitrary files.
→ Look for: user input used in file paths. → Fix: canonicalize and validate
paths; allowlist; never join user input straight into a filesystem path.

**23. Server-side request forgery (SSRF)** — server fetches a URL the user
controls, reaching internal services / cloud metadata.
→ Fix: allowlist destinations; block internal IP ranges and metadata endpoints;
validate the resolved address, not just the string.

---

## 5. Sessions, tokens & cookies

**24. Broken password reset flows** — guessable/non-expiring reset tokens, host-
header poisoning, reset without verification.
→ Fix: cryptographically random single-use tokens with short expiry; verify
ownership; don't leak whether an account exists.

**25. Weak session management** — sessions that don't expire, no rotation on
login, predictable IDs.
→ Fix: rotate session on login/privilege change; expire idle sessions; use the
auth provider's session handling.

**26. JWT secrets that are weak, leaked, or reused** — `secret`, committed
signing keys, `alg:none` accepted.
→ Fix: strong random signing secret in env; reject `alg:none`; verify
signature and expiry; rotate keys.

**27. Overly permissive CORS** — `Access-Control-Allow-Origin: *` with
credentials, or reflecting any origin.
→ Fix: allowlist specific trusted origins; don't combine wildcard origin with
credentials.

**47. Cookies missing HttpOnly, Secure, or SameSite** — session cookies
readable by JS, sent over HTTP, or cross-site.
→ Fix: set `HttpOnly`, `Secure`, and `SameSite=Lax/Strict` on session cookies.

---

## 6. Frontend trust boundary

The client is controlled by the attacker. Anything enforced only there isn't
enforced.

**15. Client-side-only security checks** — auth/role/validation done only in JS.
→ Fix: treat client checks as UX; re-enforce everything server-side.

**32. Payment or subscription checks only done on the frontend** — premium
features unlocked by client state.
→ Look for: `if (user.isPro)` gating with no server enforcement. → Fix: verify
entitlement server-side on every protected request and via webhook-confirmed
billing state.

---

## 7. Rate limiting & exposure surface

**28. Rate limits missing on login, signup, APIs, and AI endpoints** — brute
force, credential stuffing, and cost-draining AI abuse.
→ Fix: rate-limit by IP/account on auth and expensive endpoints; add CAPTCHA /
exponential backoff; cap AI usage per user.

**29. Public test or staging environments** — staging exposed, often with real
data and weaker auth.
→ Fix: lock staging behind auth/VPN/allowlist; never use prod data unmasked;
noindex.

**30. Default credentials left unchanged** — `admin/admin`, sample keys.
→ Fix: change all defaults; remove sample/seed accounts before launch.

**31. Webhook endpoints without signature verification** — anyone can POST fake
webhook events (fake "payment succeeded").
→ Fix: verify the provider's signature on every webhook; reject unsigned.

**45. Publicly exposed internal dashboards** — admin panels, metrics, queue
UIs, DB admin tools open to the internet.
→ Fix: put behind auth/VPN; don't expose tooling (Adminer, Bull board, etc.)
publicly.

---

## 8. AI-specific

**39. Prompt injection in AI features** — user input manipulates the model's
instructions to exfiltrate data or misbehave.
→ Fix: separate trusted instructions from untrusted input; don't put secrets in
prompts; constrain and validate model output; treat model output as untrusted.

**40. AI tools/actions allowed to access data without permission checks** — the
agent can read/write anything the backend can, ignoring the user's authz.
→ Fix: run AI tool calls under the user's own permissions; authorize every
tool action server-side; never give the agent ambient admin access.

---

## 9. Ops, logging & dependencies

**35. Logs containing tokens, emails, passwords, or private user data** — PII
and secrets written to logs.
→ Fix: redact secrets/PII before logging; never log full tokens or passwords.

**37. Dependency vulnerabilities** — known-CVE packages.
→ Fix: run `npm audit` / `pip-audit` / Dependabot; patch promptly.

**38. Outdated packages** — stale deps accumulating risk.
→ Fix: keep dependencies current; automate update PRs.

**42. No audit logs** — no record of who did what; can't investigate.
→ Fix: log security-relevant events (logins, role changes, data access) with
actor, action, timestamp.

**43. No monitoring or alerting** — breaches go unnoticed.
→ Fix: alert on anomalies — error spikes, auth failures, unusual data access.

**44. No backup or restore plan** — ransomware/accident = total loss.
→ Fix: automated, tested, off-site backups; rehearse restore.

**46. Missing security headers** — no HSTS, CSP, X-Content-Type-Options,
X-Frame-Options.
→ Fix: set standard security headers (e.g. via helmet or platform config).

**48. Unencrypted sensitive data** — sensitive data stored/transmitted in clear.
→ Fix: TLS everywhere; encrypt sensitive fields at rest; hash passwords with a
strong algorithm (bcrypt/argon2), never plaintext or fast hashes.

**50. Over-trusting generated code without review** — shipping AI output
unreviewed. This is the meta-vuln behind all the others.
→ Fix: review generated code for the issues above before it goes live; this
checklist is that review.
