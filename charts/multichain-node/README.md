# multichain-node

![Version: 0.3.1](https://img.shields.io/badge/Version-0.3.1-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.2.0](https://img.shields.io/badge/AppVersion-0.2.0-informational?style=flat-square)

A Helm chart for the SensRNet multichain node

**Homepage:** <https://github.com/kadaster-labs/sensrnet-home>

## Source Code

* <https://github.com/kadaster-labs/sensrnet-multichain>

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity for pod assignment (evaluated as a template) |
| fullnameOverride | string | `""` | String to fully override multichain-node.fullname |
| image.pullPolicy | string | `"IfNotPresent"` | SensRNet multichain-node image pull policy |
| image.repository | string | `"sensrnetnl/multichain-node"` | SensRNet multichain-node image name |
| image.tag | string | `""` | SensRNet multichain-node image tag |
| ingress.annotations | object | Check `values.yaml` file | Ingress annotations (evaluated as a template) |
| ingress.enabled | bool | `true` | Enable ingress controller resource |
| ingress.routes | list | Check `values.yaml` file | Ingress routes (evaluated as a template) |
| nameOverride | string | `""` | String to partially override multichain-node.fullname |
| nodeSelector | object | `{}` | Node labels for pod assignment (evaluated as a template) |
| persistence.accessModes | list | `["ReadWriteOnce"]` | PVC Access Mode for multichain-node data volume |
| persistence.annotations | object | `{}` | Additional PVC annotations |
| persistence.enabled | bool | `true` | Enable multichain persistence using PVC |
| persistence.size | string | `"1Gi"` | PVC Storage Request for multichain-node data volume |
| podSecurityContext | object | `{}` | SensRNet multichain-node pods' Security Context |
| resources | object | `{}` | The requested resources and resources limits for the Multichain-node container (evaluated as a template) |
| securityContext | object | `{}` | SensRNet multichain-node containers' Security Context |
| service.annotations | object | `{}` | Service annotations (evaluated as a template) |
| service.type | string | `"ClusterIP"` | Kubernetes Service type |
| settings.chainName | string | `"SensRNet"` | Name of the chain to create or connect to |
| settings.connectToExistingChain | bool | `false` | Whether to connect to an existing chain, or create a new one |
| settings.mainNodeHost | string | `""` | Hostname or IP address of the multichain node this node needs to be connected to. Is ignored if connectToExistingChain is false |
| settings.p2pPort | int | `8571` | Port used for external access |
| settings.rpc.password | string | `"password"` | JSON-RPC API password |
| settings.rpc.port | int | `8570` | Port used for internal (Kubernetes Cluster) JSON-RPC API |
| settings.rpc.username | string | `"username"` | JSON-RPC API username |
| tolerations | list | `[]` | Tolerations for pod assignment (evaluated as a template) |

