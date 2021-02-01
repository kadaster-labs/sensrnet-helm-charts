# Helm Charts

## Requires:
- Kubernetes cluster
- Helm >= 3.2
- Cluster credentials in kubeconfig

## Installation
The SensRNet application has Traefik with additional settings as dependency, so install it first:
```
helm install traefik traefik/traefik \
  --set ports.multichain.port=8571 \
  --set ports.multichain.expose=true \
  --set ports.multichain.exposedPort=8571 \
  --set ports.multichain.protocol=TCP
```

Then, the individual components can be installed. The charts assume that you run >= 3 nodes for pod scheduling of MongoDB and EventStore databases. On testing environments containing less nodes, changes the Eventstore clusterSize in `charts/registry-backend/values.yaml`. Normally, this would be done on the command line, but there is a bug in the EventStore charts. 

Currently, the chart packages are not in a registry yet, so you'll have to clone this repo before continuing
```
git clone git@github.com:kadaster-labs/sensrnet-helm-charts.git
cd sensrnet-helm-charts
```
### Multichain

Fill in the correct mainNodeHost to connect to the SensRNet blockchain.
```
helm upgrade --install multichain-node charts/multichain-node/ \
  --set settings.connectToExistingChain=true \
  --set settings.mainNodeHost=<MAIN_HOST>
```

The other components can be installed (using the default values) using:
```
helm upgrade --install registry-backend charts/registry-backend/
helm upgrade --install sync-bridge charts/sync-bridge/
helm upgrade --install registry-frontend charts/registry-frontend/
```

Other overridable values can be found in the respective folders.

Before you can start sharing data with the network, you'll first need sending permissions, as a node will have read-only access by default. First, find the wallet address of your MultiChain pod. Then, share this address with the network admins. Once they've given you sending permissions, you can start participating in the SensRNet distributed ledger.

```
kubectl get pods -A
kubectl exec -n registry-node-stack <POD_NAME> -- multichain-cli -datadir=/data SensRNet getaddresses
```

## Find Us

* [GitHub](https://github.com/kadaster-labs/sensrnet-home)

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Maintainers <a name="maintainers"></a>

Should you have any questions or concerns, please reach out to one of the project's [Maintainers](./MAINTAINERS.md).

## License

This work is licensed under a [EUPL v1.2 license](./LICENSE.md).
