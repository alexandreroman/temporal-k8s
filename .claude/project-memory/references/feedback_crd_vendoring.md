---
name: CRD vendoring workflow
description: >-
  How to bump and maintain the vendir-managed CRD
  bundles under crds/
type: feedback
---

# CRD vendoring workflow

The `crds/` layer vendors three upstream CRD bundles via vendir
(config in `vendir.yml` at the repo root):

- **cert-manager** (`githubRelease`, pinned tag) — single release
  artifact `cert-manager.crds.yaml`, referenced directly from
  `crds/kustomization.yaml`.
- **cloudnative-pg** (`git`, pinned tag) — only `config/crd/bases/**`
  is synced (upstream `kustomization.yaml` ships dead-code patch
  references). A local `crds/cloudnative-pg/kustomization.yaml`
  lists every CRD file explicitly.
- **gateway-api** (`git`, pinned tag) — `config/crd/**` minus
  `experimental/**`. Upstream's own `kustomization.yaml` is used
  directly via `crds/kustomization.yaml` → `gateway-api/upstream`.

When bumping any of these versions:

1. Bump the `tag` (or `ref`) in `vendir.yml`.
2. Run `task vendir-sync`. The cert-manager `githubRelease` source
   calls the GitHub API, which rate-limits anonymous calls
   (HTTP 403); run
   `VENDIR_GITHUB_API_TOKEN=$(gh auth token) task vendir-sync`
   when that happens.
3. **cnpg only**: diff the file list under
   `crds/cloudnative-pg/upstream/` against the previous set; if
   upstream added or removed a CRD, update
   `crds/cloudnative-pg/kustomization.yaml` to match. cert-manager
   and gateway-api don't need this check — cert-manager is a single
   file, gateway-api uses upstream's own kustomization.yaml.
4. Verify that `kustomize build crds/` emits the expected objects.
   As of cert-manager v1.21.2 / cnpg v1.30.1 / gateway-api v1.6.3:
   27 CRDs, 2 HelmRepositories, 2 HelmReleases, plus the Gateway API
   `safe-upgrades` ValidatingAdmissionPolicy and its Binding. The CRD
   count grows whenever upstream adds a CRD (a new cnpg kind, a
   Gateway API kind graduating to the standard channel). Run
   `yamllint .`.
5. `vendir.lock.yml` is auto-managed — commit it alongside the bump.

prometheus-operator and temporal-worker-controller CRDs are NOT
vendored — they come from dedicated Helm charts
(`prometheus-operator-crds`, `temporal-worker-controller-crds`)
declared as HelmReleases under `crds/`. Bumping those is a
chart-version bump, with no vendir involvement.

**Why:** cnpg's local `kustomization.yaml` is a hand-curated index of
CRD files. If a CNPG release adds a CRD and the index is not updated,
that CRD silently won't deploy — the Flux pipeline does not fail fast
on this, it just renders fewer resources than expected.

**How to apply:** any time work touches a vendir-managed CRD
(upgrading a tool's pinned version, refreshing the lockfile,
debugging a missing CRD), follow the bump procedure above. Step 3
(cnpg index check) is the load-bearing one — skipping it is the
realistic failure mode.
