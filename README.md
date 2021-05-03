# Helm Charts
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/sensrnet)](https://artifacthub.io/packages/search?repo=sensrnet)

## Requires:
- Kubernetes cluster
- Helm >= 3.2
- Cluster credentials in kubeconfig

Please note that if you're using other Helm release names from the ones described in this document, make sure they only contain lower case letters and numbers, and may only be separated by dashes. Please refer to the (Helm docs)[https://helm.sh/docs/chart_best_practices/conventions/#chart-names] for further info.

## Installation

First create a separate namespace for the **SensRNet Registry Node**:

```bash
kubectl create namespace sensrnet-registry
```

The SensRNet application has Traefik with additional settings as dependency, so install it first:

```bash
helm repo add traefik https://helm.traefik.io/traefik
helm repo update

helm install -n sensrnet-registry traefik traefik/traefik \
  --set ports.multichain.port=8571 \
  --set ports.multichain.expose=true \
  --set ports.multichain.exposedPort=8571 \
  --set ports.multichain.protocol=TCP \
  --set service.spec.externalTrafficPolicy=Local
```

### OpenID Connect
The SensRNet stack is constructed in such way that you plug in your own OpenID Connect (OIDC) provider. While you could theoretically plug in the OIDC parameters of your providers into the frontend and backend, we recommend using [Dex](https://dexidp.io/). The default deployments assume integration with Dex. You can define the OIDC connections there and it provides a standardized interface for SensRNet to program against.

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

### Using the chart repo

> :warning: The charts assume that you run >= 3 nodes for pod scheduling of MongoDB and EventStore databases. Setting the clusterSize of Eventstore using `--set` is not working, we're investigating a fix. On testing environments containing less nodes, please proceed to "Using the raw charts".
>

**1.** The chart packages are are hosted on GitHub Pages, so you can add that repo.

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

> :warning: We currently have some trouble getting the EventStore database to initialize properly when deploying in a cloud environment. For this reason, we've divided the deployment step in two parts.

**3A.** Deploy the databases (EventStore and MongoDB) first.

```bash
helm upgrade --install -n sensrnet-registry registry-backend sensrnet/registry-backend
```

Monitor the status of the `registry-backend-eventstore` and `registry-backend-mongodb` pods. Please wait continuing to the next step until all replicas have are running and ready. This might take a couple of minutes. Inspect the dashboard or use the following command to monitor the status:

```bash
kubectl get pods -w
```

**3B.** Deploy the other components

Once the databases are up and running, the other components can safely be installed. If you skipped 3A this might still work, but we cannot guarantee it.

The components can be installed (using the default values) using the following commands. Other overridable values can be found in the respective folders.

```bash
helm upgrade -n sensrnet-registry --install registry-backend sensrnet/registry-backend \
  --set replicaCount=1 \
  --set ingress.routes[0].match="Host(\`<YOUR-SENSRNET-DOMAIN>\`) && PathPrefix(\`/api/\`)" \
  --set "settings.oidc_issuer=<YOUR-SENSRNET-DOMAIN>/dex" \
  --set "settings.oidc_jwks_url=<YOUR-SENSRNET-DOMAIN>/dex/keys"
helm upgrade -n sensrnet-registry --install sync-bridge sensrnet/sync-bridge
helm upgrade -n sensrnet-registry --install registry-frontend sensrnet/registry-frontend \
  --set ingress.routes[0].match="Host(\`<YOUR-SENSRNET-DOMAIN>\`) && PathPrefix(\`/\`)" \
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
