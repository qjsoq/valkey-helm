# Valkey Helm Charts

Helm charts for Valkey on Kubernetes.

This repository is a fork of [valkey-io/valkey-helm](https://github.com/valkey-io/valkey-helm).
It exists to maintain **`valkey-cluster`**, an operator-free chart for Valkey in cluster
mode. Upstream supports only standalone and sentinel modes and has declined to take
cluster-mode support, so the chart lives here until the Valkey operator is production
ready. The other three charts are upstream's and are carried unchanged.

| Chart | Description | Maintained here |
|---|---|---|
| [valkey-cluster](valkey-cluster/) | Cluster mode without an operator | **yes** |
| [valkey](valkey/) | Standalone / replication without an operator | no, upstream |
| [valkey-operator](valkey-operator/) | Installs the valkey-operator | no, upstream |
| [valkey-resources](valkey-resources/) | Operator-managed CRs (ValkeyCluster) | no, upstream |

---

## TL;DR

`valkey-cluster` is published to GitHub Container Registry:

```bash
helm install valkey-cluster oci://ghcr.io/thingsboard/valkey-cluster --version 0.11.0
```

Or from a checkout of this repository:

```bash
helm install valkey-cluster ./valkey-cluster -f my-values.yaml
```

The upstream charts are not published from this fork. Install them from
[upstream](https://github.com/valkey-io/valkey-helm) instead.

---

## Prerequisites

* Kubernetes 1.20+
* Helm 3.8+ (3.8 is where OCI registry support became stable; 3.5+ is enough if you
  install from a checkout)

---

## valkey-cluster

Deploys a Valkey cluster as a StatefulSet, with no operator and no sidecar controller.
Cluster formation and recovery are handled by init and lifecycle scripts in the chart.

* 6 nodes minimum — 3 primaries and 3 replicas. Enforced by `values.schema.json`.
* Data sharded across primaries, each with one replica.
* Automatic failover when a primary becomes unavailable.
* Stable per-pod hostnames so the cluster bus reconnects after pod IPs change.
* Optional metrics via `redis_exporter`.

Configuration reference: [`valkey-cluster/values.yaml`](valkey-cluster/values.yaml).
Values are validated against [`valkey-cluster/values.schema.json`](valkey-cluster/values.schema.json).

### Publishing a new version

Bump `version` in `valkey-cluster/Chart.yaml` first. Registry tags are mutable, so
re-pushing an existing version replaces it silently.

```bash
gh auth refresh -h github.com -s write:packages
echo "$(gh auth token)" | helm registry login ghcr.io -u <github-login> --password-stdin
helm package valkey-cluster
helm push valkey-cluster-<version>.tgz oci://ghcr.io/thingsboard
```

Push access follows write access to this repository.

---

## Development

```bash
just test        # helm unittest, all charts
just lint        # helm lint, all charts
just validate    # lint + test
just package     # helm package, all charts
```

`just` is the [casey/just](https://github.com/casey/just) command runner, not a Helm
feature. Unit tests need the [helm-unittest](https://github.com/helm-unittest/helm-unittest)
plugin.

---

## Maintainers

### valkey-cluster

| Name | GitHub |
| --- | --- |
| Dmytro Artamonov | [@qjsoq](https://github.com/qjsoq) |
| Volodymyr Babak | [@volodymyr-babak](https://github.com/volodymyr-babak) |

### Upstream charts

| Name | Email |
| --- | --- |
| mk-raven | maikebit at gmail.com |
| sgissi | silvio at gissilabs.com |
| jdheyburn | joseph.heyburn at braze.com |
| Bloodraven21 | ishanij10115 at gmail.com |

---

## Slack

Upstream feedback and discussion: the
[#valkey-helm](https://valkey-oss-developer.slack.com/archives/C09JZ6N2AAV) channel in the
Valkey OSS Developer Slack. Note that cluster-mode questions about this fork belong here,
not upstream.

---

## License

BSD 3-Clause License
