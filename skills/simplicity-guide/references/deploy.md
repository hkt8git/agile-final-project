# Deploy

The ladder for how you ship and run the app. Start at one box; climb only when a
stated requirement forces it.

## one-box

A single machine running the app as a process (systemd, a runtime, or a PaaS).

- **Use when:** one host can carry the load; a single process (or a few) is
  enough and brief restarts are acceptable.
- **Climb when:** you need reproducible environments, isolated dependencies, or
  identical artifacts across dev/staging/prod.
- **Cost:** none beyond the host. No image registry, no scheduler — deploy is
  copy-and-restart.
- **Rationale template:** `"chose one box — {single host carries load},
  {few processes}, {restarts acceptable}"`

## containers

The app packaged as a container image, run on one or a few hosts.

- **Use when:** you need reproducible, isolated, portable artifacts — pinned
  dependencies and the same image from dev to prod — on a small number of hosts.
- **Climb when:** you need to schedule many containers across a fleet —
  self-healing, autoscaling, rolling deploys, service discovery.
- **Cost:** a build/registry pipeline and container runtime to maintain; more
  moving parts than a bare process, but no cluster to operate.
- **Rationale template:** `"chose containers — {reproducible artifacts},
  {isolated deps}, {few hosts}"`

## orchestration

A container orchestrator (Kubernetes, Nomad, ECS) scheduling across a fleet.

- **Use when:** you run many services/containers needing scheduling,
  self-healing, autoscaling, rolling deploys, and service discovery at scale.
- **Climb when:** (top of this ladder) — multi-cluster and multi-region topology
  is a separate, later decision driven by measured scale.
- **Cost:** a cluster to run, secure, upgrade, and staff. Heavy ops burden —
  check it hard against the maintainer ceiling before climbing.
- **Rationale template:** `"chose orchestration — {many services},
  {self-healing and autoscaling}, {scale beyond a few hosts}"`
