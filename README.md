# xpu-resource-topology

For using the xpu resource topology:

```
kubectl apply -k deployment/xpu-resource-topology
```

For testing purpose:

```
kubectl apply -k deployment/test-yaml
```

If your cluster don't have xpu device plugin installed, you should deploy it with the yaml under the deployment/xpu-device-plugin, choose either dedicated mode or share mode.

