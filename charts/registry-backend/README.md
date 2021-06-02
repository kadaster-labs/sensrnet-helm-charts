# registry-backend

![Version: 0.3.4](https://img.shields.io/badge/Version-0.3.3-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.4.0](https://img.shields.io/badge/AppVersion-0.4.0-informational?style=flat-square)

A Helm chart for the SensRNet registry back-end

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-registry-backend>

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| https://charts.bitnami.com/bitnami | mongodb | 10.6.0 |
| https://eventstore.github.io/EventStore.Charts | eventstore | 0.2.5 |

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[0].key | string | `"app.kubernetes.io/name"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[0].operator | string | `"In"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[0].values[0] | string | `"eventstore"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[1].key | string | `"app.kubernetes.io/component"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[1].operator | string | `"In"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].labelSelector.matchExpressions[1].values[0] | string | `"database"` |  |
| eventstore.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution[0].topologyKey | string | `"kubernetes.io/hostname"` |  |
| eventstore.clusterSize | int | `3` |  |
| eventstore.enabled | bool | `true` |  |
| eventstore.eventStoreConfig.EVENTSTORE_CLUSTER_DNS | string | `"registry-backend-eventstore"` |  |
| eventstore.eventStoreConfig.EVENTSTORE_DISABLE_EXTERNAL_TCP_TLS | bool | `true` |  |
| eventstore.eventStoreConfig.EVENTSTORE_DISCOVER_VIA_DNS | bool | `true` |  |
| eventstore.eventStoreConfig.EVENTSTORE_ENABLE_EXTERNAL_TCP | bool | `true` |  |
| eventstore.eventStoreConfig.EVENTSTORE_RUN_PROJECTIONS | string | `"All"` |  |
| eventstore.eventStoreConfig.EVENTSTORE_START_STANDARD_PROJECTIONS | bool | `true` |  |
| eventstore.imageTag | string | `"release-5.0.9"` |  |
| eventstore.persistence.enabled | bool | `true` |  |
| eventstore.persistence.size | string | `"12Gi"` |  |
| eventstore.scavenging.enabled | bool | `true` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"sensrnetnl/registry-backend"` |  |
| image.tag | string | `""` |  |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `true` |  |
| ingress.host | string | `"sensrnet.local"` |  |
| ingress.path | string | `"/api"` |  |
| ingress.tls | list | `[]` |  |
| mongodb.architecture | string | `"replicaset"` |  |
| mongodb.auth.enabled | bool | `false` |  |
| mongodb.enabled | bool | `true` |  |
| mongodb.imageTag | string | `"4.4.3"` |  |
| mongodb.persistence.enabled | bool | `true` |  |
| mongodb.persistence.size | string | `"10Gi"` |  |
| mongodb.podAntiAffinityPreset | string | `"hard"` |  |
| mongodb.replicaCount | int | `3` |  |
| mongodb.service.port | int | `27017` |  |
| mongodb.service.portName | string | `"mongo-service"` |  |
| mongodb.service.type | string | `"ClusterIP"` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podSecurityContext.fsGroup | int | `2000` |  |
| replicaCount | int | `0` |  |
| resources | object | `{}` |  |
| securityContext.capabilities.drop[0] | string | `"ALL"` |  |
| securityContext.readOnlyRootFilesystem | bool | `true` |  |
| securityContext.runAsNonRoot | bool | `true` |  |
| securityContext.runAsUser | int | `1000` |  |
| service.port | int | `80` |  |
| service.type | string | `"ClusterIP"` |  |
| settings.eventstore.host | string | `"registry-backend-eventstore"` |  |
| settings.eventstore.port | string | `"ext-tcp-port"` |  |
| settings.mongo.database | string | `"sensrnet"` |  |
| settings.mongo.host | string | `"registry-backend-mongodb-headless"` |  |
| settings.mongo.port | int | `27017` |  |
| settings.oidc_audience | string | `"registry-frontend"` |  |
| settings.oidc_issuer | string | `""` |  |
| settings.oidc_jwks_url | string | `""` |  |
| settings.requireAuthentication | bool | `true` |  |
| tolerations | list | `[]` |  |

