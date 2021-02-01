# Helm Charts

## Requires:
- Kubernetes cluster
- Helm >= 3.2
- Cluster credentials in kubeconfig

## Sensrnet stack
Registry-node contains all components needed for a SensRNet Registry Node. These charts assume that you run >= 3 nodes in your cluster. The charts for individual components can be found in their respective folders.
```
git clone git@github.com:kadaster-labs/sensrnet-helm-charts.git
cd sensrnet-helm-charts

helm upgrade --install registry-node registry-node/
```
The values can be found in `registry-node/values.yaml`.

Before you can start sharing data with the network, you'll first need sending permissions, as a node will have read-only access by default. First, find the wallet address of your MultiChain pod. Then, share this address with the network admins. Once they've given you sending permissions, you can start participating in the SensRNet distributed ledger.
```
kubectl get pods -A
kubectl exec -n registry-node <POD_NAME> -- multichain-cli -datadir=/data SensRNet getaddresses
```
