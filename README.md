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
  Kubernetes cluster
- [Task](https://taskfile.dev/) — task runner

## Prerequisites

- [Docker](https://www.docker.com/) or
  [Podman](https://podman.io/)
- [Kind](https://kind.sigs.k8s.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Flux CD CLI](https://fluxcd.io/flux/cmd/)
  (`brew install fluxcd/tap/flux`) — optional,
  only needed for `task reconcile`
- [Task](https://taskfile.dev/)

## Quick start

Bootstrap a local cluster with Flux CD:

```sh
task cluster-create
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

## Accessing Temporal

Once the cluster is up and Flux has reconciled all
resources, Temporal is available through Traefik.

**Web UI** — browse workflows, schedules, and
namespaces:

```text
http://temporal.127-0-0-1.nip.io
```

**gRPC frontend** — connect workers and the
Temporal CLI on the standard port 7233:

```sh
temporal workflow list \
  --address temporal.127-0-0-1.nip.io:7233
```

> [!NOTE]
> The gRPC frontend is exposed over h2c (HTTP/2
> cleartext) through a dedicated Traefik entrypoint
> on port 7233 — no TLS configuration is required.

## Monitoring

Prometheus, Grafana, and an OpenTelemetry Collector
are deployed automatically by Flux CD.

**Grafana** — dashboards and metric exploration
(anonymous admin access, no login required):

```text
http://grafana.127-0-0-1.nip.io
```

Prometheus is pre-configured as the default
datasource.

**Prometheus** — raw metrics and PromQL queries:

```text
http://prometheus.127-0-0-1.nip.io
```

**OpenTelemetry Collector** — receives OTLP
telemetry and pushes metrics to Prometheus via
remote write. The HTTP receiver is exposed on
its standard port through Traefik:

```sh
curl http://otel.127-0-0-1.nip.io:4318/v1/metrics
```

Inside the cluster, the collector is also
reachable at:

- `otel-collector.opentelemetry:4317`
  (gRPC)
- `otel-collector.opentelemetry:4318`
  (HTTP)

## Tear down

To tear down the cluster:

```sh
task cluster-delete
```

## Project structure

```text
bootstrap/
  kind/             # Kind cluster + Flux bootstrap
infra/
  cert-manager/     # TLS certificate management
  cloudnative-pg/   # PostgreSQL operator
  gateway-api/      # Gateway API CRDs
  grafana/          # Dashboards & visualization
  opentelemetry-collector/ # OTel Collector
  prometheus/       # Metrics collection
  traefik/          # Ingress controller
temporal/
  server/           # Temporal server + database
  worker-controller/ # Temporal Worker Controller
apps/
  hello/            # Sample app for smoke testing
```

## Contributing

Contributions are welcome! Feel free to open an
issue or submit a pull request.

## License

Apache License 2.0 — see [LICENSE](LICENSE).
