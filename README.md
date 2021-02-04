# Helm Charts

## Requires:
- Kubernetes cluster
- Helm >= 3.2
- Cluster credentials in kubeconfig

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
helm upgrade --install -n sensrnet-registry registry-backend sensrnet/registry-backend \
  --set replicaCount=0
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
  --set replicaCount=1
helm upgrade -n sensrnet-registry --install sync-bridge sensrnet/sync-bridge
helm upgrade -n sensrnet-registry --install registry-frontend sensrnet/registry-frontend
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
## Find Us

* [GitHub](https://github.com/kadaster-labs/sensrnet-home)

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Maintainers <a name="maintainers"></a>

Should you have any questions or concerns, please reach out to one of the project's [Maintainers](./MAINTAINERS.md).

## License

This work is licensed under a [EUPL v1.2 license](./LICENSE.md).
