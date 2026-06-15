---
name: slopsec
description: >-
  Security audit and hardening for vibe-coded SaaS apps. Use when reviewing,
  securing, hardening, or pen-testing an app — especially AI-generated /
  vibe-coded / "shipped fast" projects (Next.js, Supabase, Firebase, Express,
  serverless). Triggers: "is my app secure", "security review", "harden this",
  "check for vulnerabilities", "I'm about to launch", "audit before deploy",
  "exposed secrets", "leaked API keys", "before I ship". Walks the 50 most
  common ways vibe-coded apps get owned and produces a prioritized fix list.
---

# slopsec

Security review built for **vibe-coded SaaS slop**: apps shipped fast with AI
assistance, where the gap between "it works" and "it's safe to expose to the
internet" is where attackers live. The premise of the source material is simple
— a freshly launched app got probed by an attacker within 3 hours. Bots scan
the whole IPv4 space constantly; "nobody knows my URL yet" is not a defense.

This skill turns 50 recurring failure modes into a repeatable audit. Use it to
**review** existing code or **harden** before a launch.

## How to use this skill

1. **Scope it.** Identify the stack (frontend framework, backend, database,
   auth provider, hosting) and where secrets live. Most slop vulns cluster
   around Supabase/Firebase rules, missing server-side authz, and leaked env.
2. **Run the audit.** Walk every item in `references/checklist.md`. For each,
   either confirm it's handled or flag it. Don't skip categories because the
   app "looks fine" — the dangerous bugs are invisible from the UI.
3. **Prove the findings.** Where safe and authorized, demonstrate the issue
   (e.g. fetch another user's row via the API, hit an admin route unauthed,
   grep the built JS bundle for secrets). A demonstrated bug gets fixed; a
   theoretical one gets argued about.
4. **Prioritize.** Score each finding by `references/severity.md`. Lead with
   the catastrophic, instantly-exploitable ones (exposed creds, missing authz,
   open DB rules). Don't bury a P0 IDOR under a missing security header.
5. **Fix and re-verify.** Apply fixes, then re-test the specific issue.

`references/principles.md` has all 50 items grouped into 9 categories with the
concrete "what to look for" and "how to fix" for each. Read it before auditing.

## The non-negotiables (read first)

These cause the majority of real-world breaches in vibe-coded apps. If you do
nothing else, verify these:

- **No secrets reachable by the client or the public.** No `.env` served, no
  keys in the frontend bundle, no source maps in prod, no secrets in git
  history, build logs, or error messages. The browser is hostile territory.
- **Every sensitive action is authorized server-side.** Authentication ("who
  are you") is not authorization ("are you allowed"). Frontend checks are UX,
  not security — the server must re-verify identity, role, ownership, and
  payment status on every request.
- **The database enforces its own access rules.** Supabase RLS / Firestore
  rules / S3 bucket policies must deny by default. "Open read/write so it works
  in dev" is how user data leaks. Tenant isolation must hold at the data layer.
- **User-controlled input is never trusted.** Validate on the server. Never
  trust IDs, roles, prices, or quantities sent by the client. Parameterize
  queries. Escape output. Verify webhook signatures.

## Categories (full detail in references/principles.md)

1. **Secrets & exposure** (1–3, 10–14, 36) — leaked creds, env, keys, debug
   pages, source maps.
2. **AuthN & AuthZ** (4–6, 9, 33–34) — missing/weak auth, no authz, IDOR,
   trusting client-supplied IDs/roles.
3. **Database & storage** (7–8, 41, 49) — open rules, misconfigured buckets,
   excessive DB perms, broken tenant isolation.
4. **Injection & input** (16–23) — SQLi, NoSQLi, XSS, CSRF, file upload, path
   traversal, SSRF, missing validation.
5. **Sessions, tokens & cookies** (24–27, 47) — reset flows, session mgmt, JWT
   secrets, CORS, cookie flags.
6. **Frontend trust boundary** (15, 32) — client-side-only checks, frontend
   payment gating.
7. **Rate limiting & exposure surface** (28–31, 45) — missing rate limits,
   public staging, default creds, unsigned webhooks, exposed dashboards.
8. **AI-specific** (39–40) — prompt injection, AI tools acting without authz.
9. **Ops, logging & dependencies** (35, 37–38, 42–44, 46, 48, 50) — secret
   leakage in logs, vuln/outdated deps, no audit/monitoring/backups, missing
   headers, unencrypted data, over-trusting generated code.

## Output

Produce a findings report: `references/report-template.md`. One row per finding
with severity, evidence, and the specific fix. End with a prioritized fix
order, not just a list.

## Scope & ethics

Only test apps you own or are explicitly authorized to test. This skill is for
**defensive** hardening and authorized review. Demonstrating a vuln on your own
app is fine; probing someone else's is not.
