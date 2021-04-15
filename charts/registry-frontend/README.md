# registry-frontend

![Version: 0.3.0](https://img.shields.io/badge/Version-0.3.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.4.0](https://img.shields.io/badge/AppVersion-0.4.0-informational?style=flat-square)

A Helm chart for Kubernetes

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-registry-frontend>

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"sensrnetnl/registry-frontend"` |  |
| image.tag | string | `""` |  |
| ingress.annotations."kubernetes.io/ingress.class" | string | `"traefik"` |  |
| ingress.enabled | bool | `true` |  |
| ingress.routes[0].match | string | `"PathPrefix(`/`)"` |  |
| ingress.tls | list | `[]` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podSecurityContext | object | `{}` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext | object | `{}` |  |
| service.port | int | `80` |  |
| service.type | string | `"ClusterIP"` |  |
| settings.apiUrl | string | `"/api"` |  |
| settings.oidc_client_id | string | `"registry-frontend"` |  |
| settings.oidc_issuer | string | `""` |  |
| tolerations | list | `[]` |  |

