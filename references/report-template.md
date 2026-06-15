# slopsec findings report

**App:** <name> · **Reviewed:** <date> · **Reviewer:** <you> · **Scope:** <what
was in/out of scope, authorization basis>

## Summary

<2–3 sentences: overall posture, count of P0/P1, the one thing to fix first.>

| Priority | Count |
|----------|-------|
| P0 | |
| P1 | |
| P2 | |
| P3 | |

## Findings

Lead with P0. One row per finding.

### [P0] <title> (principle #N)
- **Where:** `path/to/file.ts:42` / endpoint / config
- **Impact:** <what an attacker gets>
- **Evidence:** <how you confirmed it — request/response, grep hit, repro steps>
- **Fix:** <specific, actionable change>
- **Secret rotation needed?** yes/no — <which>

### [P1] ...

(repeat)

## Prioritized fix order

1. <P0 …>
2. <P0 …>
3. <P1 …>

## Not applicable / verified safe

<Brief note on categories checked and found clean, so the report shows full
coverage, not just problems.>
