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
- **Flux CD**: GitOps-based deployment

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
