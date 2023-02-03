# xpu-resource-topology

### Prerequisite

If your cluster don't have xpu device plugin installed, you should deploy it with the yaml under the deployment/xpu-device-plugin, choose either dedicated mode or shared mode.

### Usage

For using the xpu resource topology:

Delete the old cpu manager state file:

```
rm -rf /var/lib/kubelet/cpu_manager_state
rm -rf /var/lib/kubelet/memory_manager_state
```

You should configure the kubelet config file (normally located at `/var/lib/kubelet/config.yaml`) by adding following arguments.
Reserved memory and cpu can be adjusted according to the real user scenario:

```
topologyManagerPolicy: "single-numa-node"
cpuManagerPolicy: "static"
reservedSystemCPUs: "0"
memoryManagerPolicy: "Static"
systemReserved: {"memory" :"512Mi"}
kubeReserved: {"memory" :"512Mi"}
evictionHard: {"memory.available": "100Mi"}
reservedMemory: [{"numaNode": 0, "limits": {"memory": "1124Mi"}}]
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

Check whether the related pod under the tas-topology-updater namespace is in running status. 

Start up the scheduler plugin for using the topology aware scheduler.

```
kubectl apply -f deployment/xpu-resource-topology/xpu-topology-aware-scheduler.yaml
```

### Testing


For testing purpose:

```
kubectl apply -f deployment/test-yaml/test-with-device.yaml
```



