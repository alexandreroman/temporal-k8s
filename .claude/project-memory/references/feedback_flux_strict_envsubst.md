---
name: "Flux strict envsubst vs OTel ${env:} syntax"
description: >-
  Escape $ as $$ in OpenTelemetry Collector config so Flux 2.9
  strict-mode postBuild substitution does not fail the infra layer
type: feedback
---

# Flux strict envsubst vs OTel ${env:} syntax

The infra Kustomization uses `postBuild.substituteFrom`
(cluster-config ConfigMap). As of Flux **2.9**, that substitution
runs in **strict mode**: any unresolved `${VAR}` in the rendered
manifests fails the whole layer.

The OpenTelemetry Collector config in
`infra/opentelemetry-collector/helmrelease.yaml` uses the
collector's own `${env:MY_POD_IP}` substitution syntax. Flux parses
that as a variable named `env` (unset) and aborts with
`envsubst error: variable substitution failed: variable not set
(strict mode): "env"`.

**Fix:** escape the dollar sign as `$${env:MY_POD_IP}`. Flux renders
`$${...}` to a literal `${...}`, so the collector receives
`${env:MY_POD_IP}` and resolves it at runtime from the `MY_POD_IP`
env var the chart injects (pod `status.podIP`).

**Why:** on Flux 2.8 the unset `env` var was silently substituted to
empty, leaving `:4317` (bind-all) which happened to work — so the
bug was latent. The 2.8.8 -> 2.9.2 upgrade turned it into a hard
failure that blocks every infra HelmRelease behind the Kustomization.
Escaping is also strictly more correct: the receiver now binds to the
pod IP as intended instead of all interfaces.

**How to apply:** any config value that must reach a downstream tool
with a literal `${...}` (OTel `${env:...}`, shell snippets, other
templating) inside a Flux-substituted layer must use `$${...}`. When
adding or editing such values, or bumping Flux, check for unescaped
`${` that is not a Flux variable. See related
[[feedback_explain_why_comments]].
