# Using Temporal with Kubernetes

> [!WARNING]
> This project is intended for testing and
> development only. It is **not** production-ready.
> No support is provided. Use at your own risk.

Temporal deployment on Kubernetes for testing purposes.

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
- [Task](https://taskfile.dev/)

## Quick start

Bootstrap a local cluster with Flux CD:

```sh
task -d bootstrap/kind-postman
```

This will:

1. Verify that required tools are installed
2. Create a Kind cluster named `temporal`
3. Wait for the cluster nodes to be ready
4. Install Flux CD into the cluster

To tear down the cluster:

```sh
task -d bootstrap/kind-postman delete
```

## Project structure

```
bootstrap/
  kind-postman/    # Kind cluster + Flux bootstrap
addons/
  certmanager/     # TLS certificate management
  temporal/        # Temporal server manifests
  traefik/         # Ingress controller
```

## Contributing

Contributions are welcome! Feel free to open an
issue or submit a pull request.

## License

Apache License 2.0 — see [LICENSE](LICENSE).
