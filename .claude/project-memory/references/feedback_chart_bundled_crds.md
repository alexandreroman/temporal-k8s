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
regardless of values — there is no chart value to disable it.
Traefik (39.0.8) is the known case: it bundles older Gateway API
CRDs. Once the project's vendored Gateway API CRDs are at v1.5+,
the new `safe-upgrades` ValidatingAdmissionPolicy rejects pre-v1.5
CRDs and the HelmRelease install fails hard. See commit `744c555`
(`infra/traefik/helmrelease.yaml`) for the canonical fix.

**How to apply:** before adopting any new chart, run
`helm pull <chart> --untar --untardir /tmp/...` and inspect the
`crds/` subfolder. If it ships CRDs at all — especially CRDs the
project already vendors — add both `install.crds: Skip` and
`upgrade.crds: Skip` proactively, with a WHY comment co-located
with each field.
