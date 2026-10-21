---
name: "E2E validation of a branch on Kind"
description: >-
  How to deploy an unmerged branch on the Kind cluster
  and which client addresses to use in E2E tests
type: feedback
---

# E2E validation of a branch on Kind

The in-cluster Flux `GitRepository` tracks `main` on GitHub, so a local
change only reaches the cluster once it is pushed. To validate a branch
end to end before merging:

1. Push the branch to `origin` (confirm with the user first).
2. Create the cluster and install Flux (`task kind:preflight
   kind:cluster-create kind:cluster-ready kind:flux-install`), then
   bootstrap with the branch patched in, live only:

   ```sh
   kustomize build bootstrap/fluxcd \
     | sed 's/branch: main/branch: <branch>/' \
     | kubectl --context kind-temporal apply -f -
   ```

3. Run `task flux-reconcile`, then exercise the stack: run workflows
   with a throwaway SDK worker, check Prometheus targets, recording rules
   and `external.metrics.k8s.io`, and drive the Temporal UI, Grafana,
   Prometheus and `hello` in a browser.

The branch patch is never committed: `bootstrap/fluxcd/gitrepository.yaml`
always points at `main`.

E2E clients use the hostname-routed addresses documented in the README:
`temporal.127-0-0-1.nip.io:7233` for gRPC and
`otel.127-0-0-1.nip.io:4318` for OTLP/HTTP. `localhost:7233` and
`localhost:4318` return HTTP 404 from Traefik, because the GRPCRoute and
HTTPRoute match on hostnames.

**Why:** pushing straight to `main` puts unvalidated versions in front of
everyone who bootstraps the project, and a 404 on `localhost` looks like a
regression when it is only a routing mismatch.

**How to apply:** use this procedure for any change that needs an
in-cluster check (dependency sweeps, chart value changes). Before calling
a connection failure a regression, check that the client targets the
nip.io hostname.
