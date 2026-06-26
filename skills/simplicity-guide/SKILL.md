# Simplicity Guide

A reference for architecture decisions. For each technical choice, locate the
relevant reference file, identify the lowest rung matching your project's
conditions, and climb only when a *stated* requirement forces it.

Six forks:

| Fork | Ladder |
|---|---|
| Data store | flat-files → sqlite → postgres |
| Frontend | server-rendered-html → html-plus-htmx → spa-framework |
| Realtime | manual-refresh → polling → sse → websockets |
| Auth | framework-sessions → auth-library → third-party-identity |
| Jobs | inline → cron → queue |
| Deploy | one-box → containers → orchestration |

## How to consult this guide

1. Locate the relevant reference file in `references/`.
2. Start at the lowest rung.
3. Climb ONLY when a *stated* requirement forces it — not "to be safe," not
   "we might need it."
4. Document the decision with a filled rationale template citing the rung.
