# 04. 使用方法

## 学习目标

能按给定流程编写、提交、观察、更新和删除一份 Deployment 与 Service 声明。

## 前提

**[Source-derived · SRC-22]** 源材料将 `kubectl` 与 YAML 声明文件列为管理集群的主流方式，并给出基础语法：`kubectl [command] [TYPE] [NAME] [flags]`。证据见 [验证记录](07-verification-record.md#src-22)。

**Evidence gap · GAP-01:** 源材料未说明如何安装、配置 `kubectl`，也未说明如何获得可访问集群；因此本节不声称这些前提已满足。

## 操作顺序

1. **[Source-derived · SRC-23]** 编写 YAML，定义 Deployment 与 Service 的期望状态。证据见 [验证记录](07-verification-record.md#src-23)。
2. **[Source-derived · SRC-24]** 使用 `kubectl apply -f demo.yaml` 提交声明。证据见 [验证记录](07-verification-record.md#src-24)。
3. **[Source-derived · SRC-25]** 用 `kubectl get pods`、`kubectl get deployments`、`kubectl get services` 查看资源；用 `kubectl describe pod <pod-name>` 看事件；用 `kubectl logs <pod-name>` 看容器日志。证据见 [验证记录](07-verification-record.md#src-25)。
4. **[Source-derived · SRC-26]** 修改 YAML 后再次 apply 更新；使用 `kubectl delete -f demo.yaml` 删除资源。证据见 [验证记录](07-verification-record.md#src-26)。

## 具体例子

**[Source-derived example · SRC-27]** 下列命令提交源材料给出的 `demo.yaml`：

```bash
kubectl apply -f demo.yaml
```

源材料的预期结果是：K8s 将集群状态向文件描述的期望状态收敛。证据见 [验证记录](07-verification-record.md#src-27)。

## 完成检查

**[Source-derived · SRC-28]** 能说明 `kubectl get` 用于查看资源状态，`describe` 用于查看 Pod 详细事件，`logs` 用于查看容器日志。证据见 [验证记录](07-verification-record.md#src-28)。

下一节：[相关工具](05-related-tools.md)。
