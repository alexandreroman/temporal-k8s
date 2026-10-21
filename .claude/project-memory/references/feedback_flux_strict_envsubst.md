---
name: "Flux strict envsubst vs OTel ${env:} syntax"
description: >-
  Escape $ as $$ in OpenTelemetry Collector config so Flux 2.9+
  strict-mode postBuild substitution does not fail the infra layer
type: feedback
---

# Flux strict envsubst vs OTel ${env:} syntax

The infra Kustomization uses `postBuild.substituteFrom`
(cluster-config ConfigMap). From Flux **2.9** on, that substitution
runs in **strict mode**: any unresolved `${VAR}` in the rendered
manifests fails the whole layer.

The OpenTelemetry Collector config in
`infra/opentelemetry-collector/helmrelease.yaml` uses the
collector's own `${env:MY_POD_IP}` substitution syntax. Unescaped,
Flux parses that as a variable named `env` (unset) and aborts with
`envsubst error: variable substitution failed: variable not set
(strict mode): "env"`.

**Fix:** escape the dollar sign as `$${env:MY_POD_IP}`. Flux renders
`$${...}` to a literal `${...}`, so the collector receives
`${env:MY_POD_IP}` and resolves it at runtime from the `MY_POD_IP`
env var the chart injects (pod `status.podIP`).

**Why:** an unescaped `${env:...}` fails the Kustomization and blocks
every infra HelmRelease behind it. Under a non-strict substitution it
would silently become empty, leaving `:4317` (bind-all), which hides
the bug. With the escape, the receiver binds to the pod IP as
intended instead of all interfaces.

**How to apply:** any config value that must reach a downstream tool
with a literal `${...}` (OTel `${env:...}`, shell snippets, other
templating) inside a Flux-substituted layer must use `$${...}`. When
adding or editing such values, or bumping Flux, check for unescaped
`${` that is not a Flux variable. See related
[[feedback_explain_why_comments]].
