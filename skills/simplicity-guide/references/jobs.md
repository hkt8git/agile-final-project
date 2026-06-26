# Background jobs

The ladder for work done outside the request. Start inline; climb only when a
stated requirement forces it.

## inline

Do the work synchronously inside the request that triggered it.

- **Use when:** the work is fast and reliable enough to finish within the
  request; the caller can wait and failure can surface to them directly.
- **Climb when:** the work must run on a schedule, or it is too slow to block
  the request but doesn't need a durable queue.
- **Cost:** none. No scheduler, no worker, no extra process — it runs where it
  is called.
- **Rationale template:** `"chose inline — {fast work}, {caller can wait},
  {no scheduling needed}"`

## cron

A scheduled task that runs the work at fixed times, out of band.

- **Use when:** the work is periodic or can be deferred to a known time —
  nightly reports, cleanups, batch syncs; occasional failure is tolerable.
- **Climb when:** you need per-event processing, retries, concurrency, or
  durability that a fire-and-forget schedule can't provide.
- **Cost:** a scheduler entry to own and monitor; missed or overlapping runs are
  on you, but no queue infrastructure.
- **Rationale template:** `"chose cron — {periodic work}, {fixed schedule ok},
  {no per-event durability}"`

## queue

A durable job queue with workers, retries, and concurrency.

- **Use when:** you need reliable per-event processing — retries on failure,
  controlled concurrency, backpressure, and jobs that survive crashes.
- **Climb when:** (top of this ladder) — multi-stage pipelines and workflow
  orchestration are a separate, later decision driven by stated needs.
- **Cost:** a queue/broker and worker processes to run, monitor, and scale.
  Real ops burden — check it against the maintainer ceiling.
- **Rationale template:** `"chose queue — {reliable per-event work},
  {retries needed}, {jobs survive crashes}"`
