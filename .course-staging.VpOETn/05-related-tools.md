# 05. 相关工具

## 学习目标

能说明源材料明确提及的操作工具与对象边界，不把未证实工具比较当作事实。

## kubectl

**[Source-derived · SRC-29]** `kubectl` 是源材料明确列出的命令行工具，用于配合 YAML 声明文件管理集群。证据见 [验证记录](07-verification-record.md#src-29)。

**[Source-derived example · SRC-30]** `kubectl apply -f demo.yaml` 提交声明；`kubectl get pods` 查看 Pod 列表和状态。证据见 [验证记录](07-verification-record.md#src-30)。

## 选择规则

**[Source-derived · SRC-31]** 当目标是通过声明式文件管理 K8s 集群时，源材料给出的工具是 `kubectl`。证据见 [验证记录](07-verification-record.md#src-31)。

## 不适用：其他工具比较

**Not applicable · GAP-02:** 源材料没有列出或比较其他容器编排工具、包管理工具、图形界面或自动化工具。缺少名称、用途、重叠点和差异的证据，故不作比较。关联依据见 [验证记录](07-verification-record.md#gap-02)。

下一节：[学习路径](06-learning-path.md)。
