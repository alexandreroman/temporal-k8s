---
name: "CloudNativePG PostgreSQL major version"
description: >-
  Keep PostgreSQL at major 16; never bump the major
  by changing the image tag during dependency upgrades
type: feedback
---

# CloudNativePG PostgreSQL major version

When upgrading dependencies, keep the CloudNativePG
PostgreSQL operand pinned at **major 16**
(`ghcr.io/cloudnative-pg/postgresql:16` in
`infra/temporal-server/cluster-postgresql.yaml`). Do
**not** raise it to 17/18 by editing the image tag,
even though CNPG ships those majors (18 is GA).

**Why:** a PostgreSQL major upgrade under CNPG is not
a tag bump — it requires an explicit offline
major-upgrade procedure (or a new cluster), so
changing `:16` to `:17`/`:18` would break the cluster
rather than upgrade it. PostgreSQL 16 is still
supported upstream and by CNPG, and this is a
test/dev cluster, so there is no pressure to move. This
is the deliberate exception to the project's otherwise
"use latest stable versions" rule (the chart/operator
and CRDs were bumped to v1.29.x; only the DB operand
major was held).

**How to apply:** during any dependency sweep, bump the
CNPG operator chart and CRDs as normal, but leave the
PostgreSQL operand at major 16 unless the user
explicitly asks for a major upgrade — and if they do,
plan the offline migration, do not just change the tag.
Decision recorded 2026-06-03.
