---
name: Temporal chart useHelmHooks default
description: >-
  Leave Temporal chart's schema.useHelmHooks at its
  default true; the chart's "set false for Flux"
  warning does not apply to this project
type: feedback
---

# Temporal chart useHelmHooks default

Do NOT override `schema.useHelmHooks` on this project's Temporal
HelmRelease. Leave the chart default (`true`).

**Why:** The official Temporal Helm chart's `values.yaml` carries
the comment `# Set to false if using Flux, Rancher or Terraform`
next to `schema.useHelmHooks`. That advice does not hold for Flux's
helm-controller, which runs Helm hooks. With `useHelmHooks: true`,
the schema migration runs as a Helm pre-install hook, so server pods
(frontend / history / matching / worker) start *after* the schema
is ready, with **zero crashloops**. Without hooks, server pods start
before the schema exists and crashloop for over a minute on every
fresh install.

**How to apply:** when bumping the Temporal chart or reading the
chart's values comments, resist the temptation to set
`schema.useHelmHooks: false` based on the README/comment. The
default is correct for this project's Flux-based deployment.
