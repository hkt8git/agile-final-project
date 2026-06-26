## This project
- **Stack:** {{stack}}
- **Requirements:** see `requirements.md` for the full recovered feature
  inventory. In one line: {{requirements}}

## Prime directive
Build the simplest thing that fully meets a stated requirement. Complexity
must be *earned* by a real, written requirement — never added "to be safe,"
"for scale we might need," or "because it's best practice."

## The complexity ladder
For every technical choice, start at the lowest rung. Climb ONLY when a
requirement in requirements.md forces it, and record why in tech-stack.md.
- Data:     files → SQLite → Postgres
- Frontend: server-rendered HTML → HTML + sprinkles (HTMX) → SPA framework
- Updates:  manual refresh → polling → SSE → websockets
- Auth:     framework sessions → library → third-party identity service
- Jobs:     do it inline → cron → a real queue
- Deploy:   one box / one process → containers → orchestration

## Maintainability ceiling
This codebase must stay maintainable by a developer at this level: {{skill_level}}.
Do not introduce tools, patterns, or abstractions above that line — no clever
metaprogramming, no premature frameworks, no build steps the owner can't debug.

## When you disagree
If you think something here blocks a genuine need, STOP and ask the developer.
Do not silently work around these rules — that is exactly what failed last time
(see anti-patterns.md).

## How to build
Work feature-by-feature from requirements.md, simplest-first. Confirm each
feature works before starting the next. Prefer boring, obvious code over
short or clever code.
