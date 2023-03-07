# XPU Resource Topology Deployment

## Usage

For using the xpu resource topology, delete old kubelet state and checkpoint file:

```
rm -rf /var/lib/kubelet/cpu_manager_state
rm -rf /var/lib/kubelet/memory_manager_state
rm -rf /var/lib/kubelet/device-plugins/kubelet_internal_checkpoint
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

## Testing

Currently we support 1, 2, 4, 8 device scheduling for r480.

For 1, 2, 4 device scheduling, the schedulerName is `topology-aware-scheduler`

For testing purpose:

```
kubectl apply -f test/xpu-resource-topology/test-with-1-device.yaml
kubectl apply -f test/xpu-resource-topology/test-with-2-device.yaml
kubectl apply -f test/xpu-resource-topology/test-with-4-device.yaml
```

You can use `kubectl exec` to exec into the related container and execute:

```
ls /dev | grep '^xpu' | sed 's/^xpu//' | head -n -1 | tr '\n' ',' | sed 's/,$//'
```

to get the corresponding xpu device id list.

For 8 device scheduling, since it will occupy all the devices on single machine, the schedulerName is
`default-scheduler`

For testing purpose:

```
kubectl apply -f test/xpu-resource-topology/test-with-8-device.yaml
```

## Cleaning up

```
kubectl delete -f deployment/xpu-resource-topology/xpu-topology-aware-scheduler.yaml
kubectl delete -f deployment/xpu-resource-topology/xpu-resource-topology-exporter.yaml
kubectl delete -f deployment/xpu-resource-topology/noderesourcetopology-crd.yaml
rm -rf /var/lib/kubelet/cpu_manager_state
rm -rf /var/lib/kubelet/memory_manager_state
rm -rf /var/lib/kubelet/device-plugins/kubelet_internal_checkpoint
```
