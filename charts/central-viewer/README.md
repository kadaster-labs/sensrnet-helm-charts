# central-viewer

![Version: 0.4.5](https://img.shields.io/badge/Version-0.4.5-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.6.7](https://img.shields.io/badge/AppVersion-0.6.7-informational?style=flat-square)

A Helm chart for the central viewer of SensRNet

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-central-viewer>

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"sensrnetnl/central-viewer"` |  |
| image.tag | string | `""` |  |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `true` |  |
| ingress.host | string | `"central-viewer.local"` |  |
| ingress.path | string | `"/"` |  |
| ingress.tls | list | `[]` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podSecurityContext | object | `{}` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext | object | `{}` |  |
| service.port | int | `80` |  |
| service.type | string | `"ClusterIP"` |  |
| settings.geoserverUrl | string | `"/geoserver/sensrnet/ows"` |  |
| tolerations | list | `[]` |  |

