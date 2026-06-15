# Severity scoring

Rank findings so the catastrophic ones don't get buried under cosmetic ones.
Score on two axes, then map to a priority.

**Impact** — what an attacker gains: full data breach / account takeover / RCE
(High) · single-user data or limited write (Medium) · info leak with no direct
exploit (Low).

**Exploitability** — effort to pull off: trivial, unauthenticated, scriptable
by a bot (High) · needs an account or some setup (Medium) · needs a chained bug
or insider position (Low).

| Priority | Meaning | Typical examples |
|----------|---------|------------------|
| **P0 — fix before launch / now** | High impact + easy to exploit | Exposed DB creds (1), open DB rules (7), public buckets (8), missing authz/IDOR (5,6,33), secrets in bundle (14), unprotected admin (9), frontend-only payment (32) |
| **P1 — fix this week** | High impact, some friction, or medium impact + easy | SQLi/XSS/SSRF (17,19,23), webhook no signature (31), weak JWT (26), broken reset (24), missing rate limits (28), prompt injection w/ data access (39,40) |
| **P2 — fix soon** | Medium impact / medium effort | CORS (27), cookie flags (47), missing validation (16), CSRF (20), verbose errors (12), tenant isolation gaps (49) |
| **P3 — hardening backlog** | Low direct impact / defense-in-depth | Security headers (46), outdated packages (38), source maps (36), audit logs (42), monitoring (43), backups (44) |

Rules of thumb:
- **Anything that exposes a secret is at least P1, usually P0** — and the fix
  always includes *rotating* the secret, not just hiding it.
- **Demonstrated > theoretical.** If you proved it on the running app, bump it
  up a level. A confirmed IDOR beats a suspected one.
- **Chains matter.** A "Low" info leak (e.g. user enumeration) that enables a
  P1 attack inherits the higher priority.
- Lead the report with P0s. One paragraph max before the first critical finding.
