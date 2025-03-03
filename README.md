# Helm Chart for Apache Nifi

[![CircleCI](https://circleci.com/gh/cetic/helm-nifi.svg?style=shield)](https://circleci.com/gh/cetic/helm-nifi/tree/master)

## Introduction

This [Helm](https://github.com/kubernetes/helm) chart installs [nifi](https://nifi.apache.org/) in a Kubernetes cluster.

## Prerequisites

- Kubernetes cluster 1.10+
- Helm 2.8.0+
- PV provisioner support in the underlying infrastructure.

## Installation

### Add Helm repository

```bash
helm repo add cetic https://cetic.github.io/helm-charts
helm repo update
```

### Configure the chart

The following items can be set via `--set` flag during installation or configured by editing the `values.yaml` directly (need to download the chart first).

#### Configure the way how to expose pgAdmin service:

- **Ingress**: The ingress controller must be installed in the Kubernetes cluster.
- **ClusterIP**: Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster.
- **NodePort**: Exposes the service on each Node’s IP at a static port (the NodePort). You’ll be able to contact the NodePort service, from outside the cluster, by requesting `NodeIP:NodePort`.
- **LoadBalancer**: Exposes the service externally using a cloud provider’s load balancer.

#### Configure the way how to persistent data:

- **Disable**: The data does not survive the termination of a pod.
- **Persistent Volume Claim(default)**: A default `StorageClass` is needed in the Kubernetes cluster to dynamic provision the volumes. Specify another StorageClass in the `storageClass` or set `existingClaim` if you have already existing persistent volumes to use.

### Install the chart

Install the nifi helm chart with a release name `my-release`:

```bash
helm install --name my-release cetic/nifi
```

## Uninstallation

To uninstall/delete the `my-release` deployment:

```bash
helm delete --purge my-release
```

## Configuration

The following table lists the configurable parameters of the pgAdmin chart and the default values.

| Parameter                                                                   | Description                                                                                                        | Default                         |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------| ------------------------------- |
| **ReplicaCount**                                                            |
| `replicaCount`                                                              | Number of nifi nodes                                                                                               | `1`                             |
| **Image**                                                                   |
| `image.repository`                                                          | nifi Image name                                                                                                    | `apache/nifi`                   |
| `image.tag`                                                                 | nifi Image tag                                                                                                     | `1.9.2`                         |
| `image.pullPolicy`                                                          | nifi Image pull policy                                                                                             | `IfNotPresent`                  |
| **SecurityContext**                                                         |
| `securityContext.runAsUser`                                                 | nifi Docker User                                                                                                   | `1000`                          |
| `securityContext.fsGroup`                                                   | nifi Docker Group                                                                                                  | `1000`                          |
| **sts**                                                                     |
| `sts.podManagementPolicy`                                                   | Parallel podManagementPolicy                                                                                       | `Parallel`                      |
| `sts.AntiAffinity`                                                          | Affinity for pod assignment                                                                                        | `soft`                          |
| **nifi properties**                                                         |
| `properties.externalSecure`                                                 | externalSecure for when inbound SSL                                                                                | `false`                         |
| `properties.isNode`                                                         | cluster node properties (only configure for cluster nodes)                                                         | `true`                          |
| `properties.httpPort`                                                       | web properties HTTP port                                                                                           | `8080`                          |
| `properties.httpsPort`                                                      | web properties HTTPS port                                                                                          | `null`                          |
| `properties.clusterPort`                                                    | cluster node port                                                                                                  | `6007`                          |
| `properties.clusterSecure`                                                  | cluster nodes sercure mode                                                                                         | `false`                         |
| `properties.needClientAuth`                                                 | nifi security client auth                                                                                          | `false`                         |
| `properties.provenanceStorage`                                              | nifi provenance repository max storage size                                                                        | `8 GB`                          |
| `properties.siteToSite.secure`                                              | Site to Site properties Secure mode                                                                                | `false`                         |
| `properties.siteToSite.port`                                                | Site to Site properties Secure port                                                                                | `10000`                         |
| `properties.siteToSite.authorizer`                                          |                                                                                                                    | `managed-authorizer`            |
| **Service**                                                                 |
| `service.headless.type`                                                     | Type of the headless service for nifi                                                                              | `ClusterIP`                     |
| `service.loadBalancer.enabled`                                              | Enable the LoabBalancerServic                                                                                      | `80`                            |
| `service.loadBalancer.httpPort`                                             | Port to expose service                                                                                             | `80`                            |
| `service.loadBalancer.httpsPort`                                            | Port to expose service in tls                                                                                      | `443`                           |
| `service.loadBalancer.annotations`                                          | Service annotations                                                                                                | `{}`                            |
| `service.loadBalancer.loadBalancerIP`                                       | LoadBalancerIP if service type is `LoadBalancer`                                                                   | `nil`                           |
| `service.loadBalancer.loadBalancerSourceRanges`                             | Address that are allowed when svc is `LoadBalancer`                                                                | `[]`                            |
| **Ingress**                                                                 |
| `ingress.enabled`                                                           | Enables Ingress                                                                                                    | `false`                         |
| `ingress.auth.enabled`                                                      | Enable Auth with Ingress                                                                                           | `false`                         |
| `ingress.auth.mode`                                                         | Auth Mode                                                                                                          | `basic`                         |
| `ingress.auth.basic_secret`                                                 | Auth Mode Basic Secret                                                                                             | `password`                      |
| **Persistence**                                                             |
| `persistence.enabled`                                                       | Use persistent volume to store data                                                                                | `true`                          |
| `persistence.storageClass`                                                  | Storage class name of PVCs                                                                                         | `standard`                      |
| `persistence.accessMode`                                                    | ReadWriteOnce or ReadOnly                                                                                          | `[ReadWriteOnce]`               |
| `persistence.dataStorage.size`                                              | Size of persistent volume claim                                                                                    | `1Gi`                           |
| `persistence.flowfileRepoStorage.size`                                      | Size of persistent volume claim                                                                                    | `10Gi`                          |
| `persistence.contentRepoStorage.size`                                       | Size of persistent volume claim                                                                                    | `10Gi`                          |
| `persistence.provenanceRepoStorage.size`                                    | Size of persistent volume claim                                                                                    | `10Gi`                          |
| `persistence.logStorage.size`                                               | Size of persistent volume claim                                                                                    | `5Gi`                           |
| `persistence.existingClaim`                                                 | Use an existing PVC to persist data                                                                                | `nil`                           |
| **jvmMemory**                                                               |
| `jvmMemory`                                                                 | bootstrap jvm size                                                                                                 | `2g`                            |
| **SideCar**                                                                 |
| `sidecar.image`                                                             | Separate image for tailing each log separately                                                                     | `ez123/alpine-tini`             |
| **Resources**                                                               |
| `resources`                                                                 | Pod resource requests and limits for logs                                                                          | `{}`                            |
| **logResources**                                                            |
| `logresources.`                                                             | Pod resource requests and limits                                                                                   | `{}`                            |
| **nodeSelector**                                                            |
| `nodeSelector`                                                              | Node labels for pod assignment                                                                                     | `{}`                            |
| **tolerations**                                                             |
| `tolerations`                                                               | Tolerations for pod assignment                                                                                     | `[]`                            |
| **zookeeper**                                                               |
|`zookeeper.enabled`                                                          | If true, deploy Zookeeper                                                                                          | `true`                          |
|`zookeeper.url`                                                              | If the Zookeeper Chart is disabled a URL and port are required to connect                                          | `nil`                           |
|`zookeeper.port`                                                             | If the Zookeeper Chart is disabled a URL and port are required to connect                                          | `2181`                          |

## Credits

Initially inspired from https://github.com/YolandaMDavis/apache-nifi.

## Todo list

* Nifi replicaCount > 1 is unstable for the moment.
* Add LDAP support.
* ...

## License

[Apache License 2.0](/LICENSE)
