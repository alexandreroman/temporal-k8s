# temporal-k8s

Temporal deployment on Kubernetes for testing and
development purposes — **not** production-ready.

See [README.md](README.md) for full documentation.

## Tech stack

- **Temporal** — workflow orchestration
- **Kubernetes** (Kind) — local cluster with one
  control-plane and multiple worker nodes
- **Flux CD** — GitOps deployment (CLI optional;
  Flux runs in-cluster)
- **CloudNativePG** — PostgreSQL operator for the
  Temporal database
- **Traefik + Gateway API** — ingress and routing
- **cert-manager** — TLS certificate management
- **kube-prometheus-stack + Grafana +
  OpenTelemetry Collector** — metrics and
  observability
- **Task** — task runner (`Taskfile.yml`)

## Build & run

```sh
task cluster-create   # create Kind cluster + Flux
task flux-reconcile   # re-trigger reconciliation
task cluster-delete   # tear down the cluster
```

## Architecture

Flux CD deploys resources in three layers, each
depending on the previous one:

- **infra/** — operators and CRDs
- **temporal/** — Temporal server, PostgreSQL
  cluster, and worker controller
- **apps/** — user-facing applications (`hello`)

The Kind cluster has one control-plane and
multiple worker nodes. Workloads must run on
worker nodes — never target the control-plane
via nodeSelector/tolerations.

## Modules

- `bootstrap/` — Kind cluster config and Flux CD
  bootstrap manifests
- `infra/` — layer 1: cert-manager, CloudNativePG,
  Gateway API, Traefik, kube-prometheus-stack,
  OpenTelemetry Collector, prometheus-adapter
- `temporal/` — layer 2: Temporal server and
  worker controller
- `apps/` — layer 3: sample applications

## Agents

Use the following agents (from the
[skillbox](https://github.com/alexandreroman/skillbox)
plugin) for all code tasks:

- **code-writer** — for ANY task that writes,
  modifies, or refactors code or YAML manifests.
  This includes one-line fixes, label changes,
  resource-limit tweaks, and annotation updates.
  Never use the Edit or Write tools directly on
  source files — always delegate to this agent.
- **code-reviewer** — for read-only code review
  before merging or when investigating issues.

## Memory

Project decisions, conventions, and context live
under `.claude/project-memory/`. Use the
**project-memory** skill (from the
[skillbox](https://github.com/alexandreroman/skillbox)
plugin) to recall relevant entries when a task
would benefit from prior context, and to persist
new decisions, deadlines, references, or
workflow preferences worth carrying across
conversations.

Do not eagerly load every entry at the start of
a conversation — consult `MEMORY.md` and pull
specific notes only when the current task
touches them.

Never use the built-in auto-memory system
(`~/.claude/projects/.../memory/`) for project
context — it is local and not shared with the
team.
