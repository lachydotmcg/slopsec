# slopsec
**Did you just get a $200 bill from AWS? Stop 'hiding' your API keys in plaintext. It sounds like you need:**
A Claude Code skill that security-audits your **vibe-coded SaaS apps**, so your slop isn't just slop, its secure slop!

Bots scan the whole internet constantly. The premise here is a real one; 
freshly launched apps can get probed by an attacker within 3 hours of going live, with this it ensures that your AI Agent isn't skipping the security checks that count.

slopsec turns **50 recurring ways vibe-coded apps get pwned** into a repeatable
audit; scope the app, walk the checklist, prove the findings, prioritize by
severity, fix, and re-verify!

Just run /slopsec for slopsec to save the day! (and your wallet)

## What's inside

| File | Purpose |
|------|---------|
| `SKILL.md` | The skill — how to run an audit, the non-negotiables, categories |
| `references/principles.md` | All 50 principles, grouped, with "what to look for" + "how to fix" |
| `references/checklist.md` | Tick-box audit you walk top to bottom |
| `references/severity.md` | P0–P3 scoring so the catastrophic stuff leads |
| `references/report-template.md` | Findings report format |

## Install

Drop the `slopsec/` folder into your skills directory:

- **Project:** `.claude/skills/slopsec/`
- **Personal:** `~/.claude/skills/slopsec/`

Then ask Claude something like "run a security review before I launch" or
"is my app secure?" and the skill triggers.

## The 9 categories

1. Secrets & exposure · 2. AuthN & AuthZ · 3. Database & storage · 4. Injection
& input · 5. Sessions, tokens & cookies · 6. Frontend trust boundary · 7. Rate
limiting & exposure surface · 8. AI-specific · 9. Ops, logging & dependencies

## Scope & ethics

For **defensive** hardening and **authorized** review only. Audit apps you own
or have explicit permission to test. Don't probe other people's apps.

## Credit

The 50 principles are adapted from a widely-shared list of common vibe-coded
app vulnerabilities. Skill structure and audit workflow are original.

## License

MIT
