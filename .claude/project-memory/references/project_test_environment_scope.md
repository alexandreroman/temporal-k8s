---
name: Test/dev environment only
description: >-
  No HA, no production-grade secret management —
  scope is local testing and development
type: project
---

# Test/dev environment only

This project is **not** production-ready. There
is no robust secret management, no high
availability, and no hardening of components.

**Why:** the goal is to bootstrap a functional
Temporal cluster on a developer laptop or in CI,
not to host real workloads. Production concerns
add complexity that defeats the purpose.

**How to apply:** when adding components, prefer
simplicity over robustness — single replicas,
plaintext secrets in manifests, default Helm
values. Do not introduce HA configurations,
external secret stores, or production-only
features unless explicitly asked.
