---
name: Temporal chart useHelmHooks default
description: >-
  Leave Temporal chart's schema.useHelmHooks at its
  default true; the chart's "set false for Flux"
  warning is outdated
type: feedback
---

# Temporal chart useHelmHooks default

Do NOT override `schema.useHelmHooks` on this project's Temporal
HelmRelease. Leave the chart default (`true`).

**Why:** The official Temporal Helm chart's `values.yaml` carries
the comment `# Set to false if using Flux, Rancher or Terraform`
next to `schema.useHelmHooks`. That advice is outdated — verified
on chart `1.2.0` + Flux `v2.8.6` + helm-controller. With
`useHelmHooks: true`, the schema migration runs as a Helm
pre-install hook, so server pods
(frontend / history / matching / worker) start *after* the schema
is ready, with **zero crashloops**. The previous chart
`1.0.0-rc.3` (no hooks) had ~75s of crashloop noise on every
fresh install.

**How to apply:** when bumping the Temporal chart or reading the
chart's values comments, resist the temptation to set
`schema.useHelmHooks: false` based on the README/comment. The
default is correct for this project's Flux-based deployment.
