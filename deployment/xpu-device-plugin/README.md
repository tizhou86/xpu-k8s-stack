# XPU Device Plugin Deployment

## 背景

XPU device plugin是昆仑芯提供的K8S昆仑设备(XPU)管理插件,该插件以Daemonset的形式运行在K8S的节点上，并提供如下几个功能:

- 以容器为粒度自动调度节点上昆仑设备资源。
- 监控节点上的昆仑设备健康状态。
- 配置昆仑设备的虚拟化功能。

## 环境依赖

XPU device plugin依赖如下几个软硬件环境:

- 昆仑设备
- 昆仑XRE软件包 >= 4.0.25
    - kunlun.ko >= 4.0.25
    - xpuml.so  >= 4.0.25
- Kubernetes >= 1.10

## 机器准备

部署XPU device plugin前，需确保node上的昆仑设备及昆仑已安装完毕，确保昆仑设备即'dev/xpu*'可被正常访问。

## 部署

创建xpu-device-plugin service account并赋权
```
kubectl apply -f deployment/xpu-device-plugin/xpu-device-plugin-rbac.yaml
```
部署XPU devcie plugin daemonset
```
kubectl apply -f deployment/xpu-device-plugin/xpu-device-plugin.yaml
```

Damonset部署成功后，可通过`$ kubectl describe node`看到每个node上的昆仑设备数量(以baidu.com/xpu标识):

```
...
Capacity:
  baidu.com/xpu:      4
  cpu:                12
  ephemeral-storage:  73364224Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             7746896Ki
  pods:               110
Allocatable:
  baidu.com/xpu:      4
  cpu:                12
  ephemeral-storage:  67612468727
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             7644496Ki
  pods:               110
...
```

## 验证

用户创建pod时可通过`baidu.com/xpu`资源类型来申请昆仑设备，具体参考test目录下示例。

