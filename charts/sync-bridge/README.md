# sync-bridge

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.1.1](https://img.shields.io/badge/AppVersion-0.1.1-informational?style=flat-square)

A Helm chart for the SensRNet sync component between SensRNet registry back-end and MultiChain node

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-sync>

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"sensrnetnl/sync-bridge"` |  |
| imagePullSecrets | list | `[]` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podSecurityContext.fsGroup | int | `2000` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext.capabilities.drop[0] | string | `"ALL"` |  |
| securityContext.readOnlyRootFilesystem | bool | `true` |  |
| securityContext.runAsNonRoot | bool | `true` |  |
| securityContext.runAsUser | int | `1000` |  |
| service.port | int | `3500` |  |
| service.type | string | `"ClusterIP"` |  |
| settings.eventstore.host | string | `"registry-backend-eventstore"` |  |
| settings.eventstore.port | string | `"ext-tcp-port"` |  |
| settings.mongo.database | string | `"sensrnet"` |  |
| settings.mongo.host | string | `"registry-backend-mongodb-headless"` |  |
| settings.mongo.port | int | `27017` |  |
| settings.multichain.host | string | `"multichain-node"` |  |
| settings.multichain.jsonRPC.password | string | `"password"` |  |
| settings.multichain.jsonRPC.username | string | `"username"` |  |
| settings.multichain.port | int | `8570` |  |
| tolerations | list | `[]` |  |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.5.0](https://github.com/norwoodj/helm-docs/releases/v1.5.0)
