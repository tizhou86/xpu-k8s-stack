# xpu-resource-topology

### Prerequisite

If your cluster don't have xpu device plugin installed, you should deploy it with the yaml under the deployment/xpu-device-plugin, choose either dedicated mode or shared mode.

### Usage

For using the xpu resource topology:

You should configure the kubelet config file (normally located at /var/lib/kubelet/config.yaml) by adding following arguments:

```
cpuManagerPolicy: static
memoryManagerPolicy: static
topologyManagerPolicy: single-numa-node

```
And then restart the Kubelet service.


Import the xpu node resource topology crd.

```
kubectl apply -f deployment/xpu-resource-topology/noderesourcetopology-crd.yaml
```

Start up the exporter for collecting topology from kubelet sock.
```
kubectl apply -f deployment/xpu-resource-topology/xpu-resource-topology-exporter.yaml

```

Start up the scheduler plugin for using the topology aware scheduler.

```
kubectl apply -f deployment/xpu-resource-topology/xpu-topology-aware-scheduler.yaml
```


### Testing


For testing purpose:

```
kubectl apply -f deployment/test-yaml/test-with-device.yaml
```



