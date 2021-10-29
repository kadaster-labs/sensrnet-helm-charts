# central-viewer-geoserver

![Version: 0.2.3](https://img.shields.io/badge/Version-0.2.3-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.2.1](https://img.shields.io/badge/AppVersion-0.2.1-informational?style=flat-square)

A Helm chart for the central viewer geoserver of SensRNet

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-central-viewer-geoserver>

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"sensrnetnl/central-viewer-geoserver"` |  |
| image.tag | string | `""` |  |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `true` |  |
| ingress.host | string | `"central-viewer.localhost"` |  |
| ingress.path | string | `"/geoserver"` |  |
| ingress.tls | list | `[]` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podSecurityContext | object | `{}` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext | object | `{}` |  |
| service.port | int | `80` |  |
| service.type | string | `"ClusterIP"` |  |
| settings.adminPw | string | `"changeit"` |  |
| settings.initialMemory | string | `"2G"` |  |
| settings.maximumMemory | string | `"4G"` |  |
| settings.mongo.database | string | `"sensrnet"` |  |
| settings.mongo.host | string | `"registry-backend-mongodb-headless"` |  |
| settings.mongo.port | int | `27017` |  |
| settings.webInterfaceDisabled | bool | `true` |  |
| tolerations | list | `[]` |  |

