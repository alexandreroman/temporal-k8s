---
name: Chart-bundled CRDs vs vendored CRDs
description: >-
  Set crds: Skip on any HelmRelease whose chart
  ships CRDs in its own crds/ folder
type: feedback
---

# Chart-bundled CRDs vs vendored CRDs

When a Helm chart ships CRDs in its `crds/` folder, set
`spec.install.crds: Skip` AND `spec.upgrade.crds: Skip` on its
HelmRelease. This is the mechanism that complements the project's
documented "CRDs owned by the dedicated crds/ layer" pattern
(CLAUDE.md) for charts that don't expose a values flag to disable
their bundled CRDs.

**Why:** Helm always installs files in `crds/` on `helm install`,
regardless of values — there is no chart value to disable it. A
chart's bundled CRDs can clash with the ones the crds layer vendors:
the Gateway API `safe-upgrades` ValidatingAdmissionPolicy rejects
Gateway API CRDs older than the vendored release, which fails the
HelmRelease install outright. Even CRDs the project never uses (such
as Traefik's `traefik.io` / `hub.traefik.io` set) would put CRD
lifecycle back in Helm's hands. `infra/traefik/helmrelease.yaml`
is the reference implementation.

**How to apply:** before adopting any new chart, run
`helm pull <chart> --untar --untardir /tmp/...` and inspect the
`crds/` subfolder. If it ships CRDs at all — especially CRDs the
project already vendors — add both `install.crds: Skip` and
`upgrade.crds: Skip` proactively, with a WHY comment co-located
with each field.
