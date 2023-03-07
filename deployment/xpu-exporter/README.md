# XPU Exporter Deployment

XPU exporter 是昆仑芯提供的K8S昆仑设备(XPU)监控插件，该插件以Daemonset的形式运行在K8S的节点上，提供监控昆仑xpu运行指标的功能.

## 环境依赖

XPU exporter 依赖如下几个软硬件环境：
- 昆仑设备
- 昆仑XRE软件包 >= 4.0.25
    - kunlun.ko >= 4.0.25
    - xpuml.so >= 4.0.25
- Kubernetes >= 1.20
- XPU device plugin


# 部署方法

部署XPU exporter前，需确保node上的昆仑设备及昆仑已安装完毕，确保昆仑设备 /dev/xpu 可被正常访问，确保xpu-device-plugin工作正常。
当前默认先读取kubelet.sock，如果读取不到，再读取docker.sock。
读取的kubelet.sock默认路径为：/var/lib/kubelet/pod-resources/kubelet.sock
读取的docker.sock默认路径为：/var/run/docker.sock
如果系统修改过默认路径，请容器启动exporter，并映射到对应路径上。
注意 ：默认使用monitor作为名字空间.


## 部署Daemonset

1. 部署xpu-exporter (资源类型 daemonset)
```
$ kubectl apply -f deployment/xpu-exporter/xpu-exporter.yaml
```
> 其中exporter.yml文件中默认使用的端口
```
 --web.listen-address=0.0.0.0:9507
```

# 监控指标metrics说明

|监控项 | metrics | 解释 | 报警阈值 | 
|---|---|---|---|
|设备数量|node_xpu_xpuDevicesNum | 【重要】检测是否掉卡 | <实际物理卡数设置 | 
|设备温度|node_xpu_temp |【重要】超过105度会触发自保降频，甚至死机 | \>105 | 
|功耗|node_xpu_powerUsage | 功耗 | 暂无 | 
|L3使用|node_xpu_l3 |在有服务运行的前提下，理论上L3用满，工作效率最好。否则可能存在环境变量没配置的情况，进而影响线上延时、兜底率| 暂无| 
|显存使用|node_xpu_mem|【重要】显存打满可能导致异常，需要看推理程序和xtcl的异常处理逻辑| \>85% | 
|xpu使用率|node_xpu_utilization |【重要】观察xpu使用率| \>80%  | 
|卡状态|node_xpu_state | 【重要】监控state状态是否报错ERROR (对应错误码207) | =207 | 

