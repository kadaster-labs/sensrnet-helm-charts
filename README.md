# Helm Charts
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/sensrnet)](https://artifacthub.io/packages/search?repo=sensrnet)

## Requires:
- Kubernetes cluster (>= 1.18)
- Helm (>= 3.2)
- Cluster credentials in kubeconfig

Please note that if you're using other Helm release names from the ones described in this document, make sure they only contain lower case letters and numbers, and may only be separated by dashes. Please refer to the (Helm docs)[https://helm.sh/docs/chart_best_practices/conventions/#chart-names] for further info.

## Installation

First create a separate namespace for the **SensRNet Registry Node**:

```bash
kubectl create namespace sensrnet-registry
```

### TCP traffic for multichain node
One of the components, the multichain node, requires an TCP exposure. By default this helm chart will expose on the internal load balancer only (a.k.a. `ClusterIP`) and external exposure needs to be configured properly with flags. There are three options available (and tested):

- Kubernetes Service Type `LoadBalancer`
- Traefik Ingress Controller
- Nginx Ingress Controller

Please apply one of these which is most applicable to your situation.

#### Kubernetes Service Type `LoadBalancer`

> :warning: Using this service type your cloud provider might charge for additional costs!

It is possible to deploy a Kubernetes service on an external available load balancer. In the helm charts the service for MultiChain has the appropriate TCP port configured. By default the service type is set to `ClusterIP` and this has to be updated to `LoadBalancer`:

```bash
helm upgrade -n sensrnet-registry --install multichain-node charts/multichain-node/ \
  --set service.type=LoadBalancer \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
```

#### Traefik 2
We currently make use of Traefik v2's IngressRouteTCP CRD, which enables the use of TCP routes. This means we assume Traefik v2 as Ingress Controller with port 8571 exposed. 

The routes are of the Traefik IngressRoute form, for example:
```ingress.routes[0].match=HostSNI(`*`)```. This is now set as default, the routing is actually done via the Entrypoint, which is the named exposed port in the Traefik v2 Ingress Controller. More information on IngressRouteTCP can be found [here](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/#kind-ingressroutetcp).

The SensRNet application assumes Traefik v2 as Ingress controller, it can be installed as followed, with the correct port exposed:

```bash
helm repo add traefik https://helm.traefik.io/traefik
helm repo update

helm install -n traefik traefik traefik/traefik \
  --set ports.multichain.port=8571 \
  --set ports.multichain.expose=true \
  --set ports.multichain.exposedPort=8571 \
  --set ports.multichain.protocol=TCP \
  --set service.spec.externalTrafficPolicy=Local
```

By default the MultiChain service is installed with a disabled ingress. For the Traefik Ingress this has to be enabled, i.e.:

```bash
helm upgrade -n sensrnet-registry --install multichain-node charts/multichain-node/ \
  --set ingress.enabled=true \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
```

`externalTrafficPolicy` is set to `Local` to perserve the client source IP ((source)[https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#preserving-the-client-source-ip]). This allows the multichain-node to record the IP adress of other connecting nodes.

#### Nginx
It is also possible to route TCP traffic using NGINX Ingress Controller. It works differently from Traefik. It does not define a custom CRD, but instead works with a ConfigMap which lists the TCP routes. More information can be found at:
https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services/

When Nginx was deployed using Helm, TCP routes can easily be added as follows:
```
helm upgrade --install -n NGINX_NAMESPACE ingress-nginx ingress-nginx/ingress-nginx \
  --set tcp.8571=MULTICHAIN_NAMESPACE/multichain-node:8571 \
  --set service.spec.externalTrafficPolicy=Local
```

This plugs the multichain service directly in the Ingress Controller. So now we only need to install the MultiChain :

```
helm upgrade -n sensrnet-registry --install multichain-node charts/multichain-node/ \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
```

TCP connections should now correctly be routed to the MultiChain pod.

### OpenID Connect
The SensRNet stack an upstream OpenID Connect (OIDC) provider as user management system. While you could theoretically plug in the OIDC parameters of your providers into the frontend and backend, we recommend using [Dex](https://dexidp.io/) as intermediate. The default deployments assume integration with Dex. You can define the OIDC connections there and it provides a standardized interface for SensRNet to program against.

Dex also supports other protocols, such as LDAP and GitHub, which can easily be plugged in if that suits your usecase better. For an exhaustive list of supported protocols, see https://dexidp.io/docs/connectors/.

An example of what your Dex deployment might look like is as follows:

```bash
helm repo add dex https://charts.dexidp.io
helm repo update

helm upgrade --install dex dex/dex \
  --namespace dex \
  --set "livenessProbe.httpPath=/dex/healthz" \
  --set "readinessProbe.httpPath=/dex/healthz" \
  --set "config.issuer=<YOUR-SENSRNET-DOMAIN>/dex" \
  --set "config.storage.type=kubernetes" \
  --set "config.storage.config.inCluster=true" \
  --set "config.staticClients[0].name=SensrnetRegistry" \
  --set "config.staticClients[0].id=registry-frontend" \
  --set "config.staticClients[0].public=true" \
  --set "config.connectors[0].type=microsoft" \
  --set "config.connectors[0].id=microsoft" \
  --set "config.connectors[0].name=Microsoft" \
  --set "config.connectors[0].config.clientID=<CLIENT_ID>" \
  --set "config.connectors[0].config.clientSecret=<CLIENT_SECRET>" \
  --set "config.connectors[0].config.redirectURI=https://<YOUR_SENSRNET_DOMAIN>/dex/callback" \
  --set "config.connectors[0].config.tenant=<TENANT_ID>" \
  --set "config.oauth2.responseTypes={code,token,id_token}" \
  --set "config.oauth2.skipApprovalScreen=true" \
  --set "ingress.enabled=true" \
  --set "ingress.hosts[0].host=<YOUR-SENSRNET-DOMAIN>" \
  --set "ingress.hosts[0].paths[0].path=/dex"
```

Then, the individual components can be installed.

### Multiple identity providers
When using Dex, it is possible to add multiple upstream identity providers. The `connectors` field in the Dex config is an array. For two Azure AD environments, deployment will look like:
```bash
  ...
  --set "config.connectors[0].type=microsoft" \
  --set "config.connectors[0].id=microsoft_a" \
  --set "config.connectors[0].name=Municipality A" \
  --set "config.connectors[0].config.clientID=<CLIENT_ID_1>" \
  --set "config.connectors[0].config.clientSecret=<CLIENT_SECRET_1>" \
  --set "config.connectors[0].config.redirectURI=https://<YOUR_SENSRNET_DOMAIN>/dex/callback" \
  --set "config.connectors[0].config.tenant=<TENANT_ID_1>" \
  --set "config.connectors[1].type=microsoft" \
  --set "config.connectors[1].id=microsoft_b" \
  --set "config.connectors[1].name=Municipality B" \
  --set "config.connectors[1].config.clientID=<CLIENT_ID_2>" \
  --set "config.connectors[1].config.clientSecret=<CLIENT_SECRET_2>" \
  --set "config.connectors[1].config.redirectURI=https://<YOUR_SENSRNET_DOMAIN>/dex/callback" \
  --set "config.connectors[1].config.tenant=<TENANT_ID_2>" \
  ...
```

Other types of connectors can be used. When multiple connectors are configured, the SensRNet login page will look as follows:
![image](https://github.com/kadaster-labs/sensrnet-helm-charts/blob/5708f8dfe35a3b0c6feea04d619bf08ba43a8b13/Dex_multiple_connectors.png)


### Using the chart repo

**1.** The Charts are are hosted on GitHub Pages, so you can add that repo.

```bash
helm repo add sensrnet https://kadaster-labs.github.io/sensrnet-helm-charts/
helm repo update
```

**2.** Fill in the correct mainNodeHost to connect to the SensRNet blockchain.

```bash
helm upgrade -n sensrnet-registry --install multichain-node sensrnet/multichain-node \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
```

**3.** Deploy the other components

```bash
helm upgrade --install -n sensrnet-registry registry-backend sensrnet/registry-backend
```

Once the MultiChain node is up and running, the other components can safely be installed. The components can be installed (using the default values) using the following commands. Other overridable values can be found in the respective folders.

```bash
helm upgrade -n sensrnet-registry --install registry-backend sensrnet/registry-backend \
  --set ingress.host=<YOUR-SENSRNET-DOMAIN> \
  --set ingress.path=/api/ \
  --set "settings.oidc_issuer=<YOUR-SENSRNET-DOMAIN>/dex" \
  --set "settings.oidc_jwks_url=<YOUR-SENSRNET-DOMAIN>/dex/keys"

helm upgrade -n sensrnet-registry --install sync-bridge sensrnet/sync-bridge

helm upgrade -n sensrnet-registry --install registry-frontend sensrnet/registry-frontend \
  --set ingress.host=demo.sensorenregister.nl \
  --set "settings.oidc_issuer=<YOUR-SENSRNET-DOMAIN>/dex"
```

### Using the raw charts
Alternatively, if you want to edit the charts directly, for example to set the number of nodes, checkout the chart repo

```bash
git clone git@github.com:kadaster-labs/sensrnet-helm-charts.git
cd sensrnet-helm-charts
```

Make your changes, then

```bash
helm upgrade -n sensrnet-registry --install multichain-node charts/multichain-node/ \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
helm upgrade -n sensrnet-registry --install registry-backend charts/registry-backend/
helm upgrade -n sensrnet-registry --install sync-bridge charts/sync-bridge/
helm upgrade -n sensrnet-registry --install registry-frontend charts/registry-frontend/
```

## Sharing sensor data

Before you can start sharing data with the network, you'll first need sending permissions, as a node will have read-only access by default. First, find the wallet address of your MultiChain pod. Then, share this address with the network admins. Once they've given you sending permissions, you can start participating in the SensRNet distributed ledger.

```bash
kubectl get pods -n sensrnet-registry
kubectl exec -n sensrnet-registry <POD_NAME> -- multichain-cli -datadir=/data SensRNet getaddresses
```
## Automatically update Chart documentation on repo updates
We use `helmlint` to lint the Helm Charts and `helm-docs` to generate the documentation. The files require a newline at the end of the file and no trailing whitespace. By installing `pre-commit` (you'll need the `pre-commit` and `helm-docs` binaries), you won't have to spend any time on the tooling and formatting the code manually at all. If you don't, CI will let it slide for now, but will catch it for you in the future - but that seems like a waste of your time!
- [pre-commit installation](https://pre-commit.com/#installation)
- [helm-docs installation](https://github.com/norwoodj/helm-docs#installation)

## Find Us

* [GitHub](https://github.com/kadaster-labs/sensrnet-home)

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Maintainers <a name="maintainers"></a>

Should you have any questions or concerns, please reach out to one of the project's [Maintainers](./MAINTAINERS.md).

## License

This work is licensed under a [EUPL v1.2 license](./LICENSE.md).
