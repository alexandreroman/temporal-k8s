# Using Temporal with Kubernetes

> [!WARNING]
> This project is intended for testing and
> development only. It is **not** production-ready.
> No support is provided. Use at your own risk.

## Goal

Provide a Kubernetes configuration to quickly deploy
a functional Temporal cluster in a test/development
environment.

## Stack

- [Temporal](https://temporal.io/) — workflow
  orchestration
- [Kubernetes](https://kubernetes.io/) — target
  deployment platform
- [Flux CD](https://fluxcd.io/) — GitOps-based
  deployment
- [Kind](https://kind.sigs.k8s.io/) — local
  Kubernetes cluster (using Podman)
- [Task](https://taskfile.dev/) — task runner

## Prerequisites

- [Podman](https://podman.io/)
- [Kind](https://kind.sigs.k8s.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Flux CD CLI](https://fluxcd.io/flux/cmd/)
  (`brew install fluxcd/tap/flux`)
- [Task](https://taskfile.dev/)

## Quick start

Bootstrap a local cluster with Flux CD:

```sh
task -d bootstrap/kind-podman
```

This will:

1. Verify that required tools are installed
2. Create a Kind cluster named `temporal`
3. Wait for the cluster nodes to be ready
4. Install Flux CD into the cluster

To trigger a full Flux CD reconciliation manually:

```sh
task reconcile
```

Verify that the cluster is working by hitting
the `hello` app through Traefik:

```sh
curl http://hello.127-0-0-1.nip.io
```

You should see `Hello from Kubernetes!`.

To tear down the cluster:

```sh
task -d bootstrap/kind-podman delete
```

## Project structure

```text
bootstrap/
  kind-podman/      # Kind cluster + Flux bootstrap
infra/
  cert-manager/     # TLS certificate management
  cloudnative-pg/   # PostgreSQL operator
  gateway-api/      # Gateway API CRDs
  traefik/          # Ingress controller
addons/
  temporal/         # Temporal server + database
apps/
  hello/            # Sample app for smoke testing
```

## Contributing

Contributions are welcome! Feel free to open an
issue or submit a pull request.

## License

Apache License 2.0 — see [LICENSE](LICENSE).
