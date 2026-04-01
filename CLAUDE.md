# temporal-k8s

Temporal deployment on Kubernetes for testing purposes.
This project is **not** production-ready.

## Goal

Provide a Kubernetes configuration to quickly deploy
a functional Temporal cluster in a test/development
environment.

## Stack

- **Temporal**: workflow orchestration
- **Kubernetes**: target deployment platform
  (Kind cluster with multiple worker nodes)
- **Flux CD**: GitOps-based deployment

## Architecture

The Kubernetes cluster has one control-plane node
and multiple worker nodes. Workloads should be
scheduled on worker nodes, not on the control-plane.
Adapt configurations accordingly (e.g. avoid
nodeSelector/tolerations that target the
control-plane node).

Flux CD deploys resources in three layers,
each depending on the previous one:

- **infra/**: operators and CRDs
  (CloudNativePG, cert-manager, Traefik,
  Gateway API)
- **temporal/**: Temporal server, PostgreSQL
  cluster, and worker controller
- **apps/**: user-facing applications (hello)

## Conventions

- All generated text and code must be in English
  (comments, commit messages, documentation,
  resource names, etc.)
- All generated text and code must be
  human-readable: lines must not exceed 80 columns
- Never use compound shell commands (&&, ||, ;).
  Run one command per Bash tool call instead.
- Kubernetes manifests use YAML format
- No robust secret management or high availability:
  this is a test environment
- Always check for the latest stable version before
  using any component (Helm chart, container image,
  CRD, tool, etc.)
- Add relevant comments in generated code and
  configuration to explain the "why" behind
  non-obvious choices
