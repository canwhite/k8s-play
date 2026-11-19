# 02. 核心概念

## 学习目标

能区分 Pod、Deployment、Service、Namespace、Node，并说明前三者如何协作。

## 心智模型

**[Source-derived · SRC-07]** 可把集群理解为统一资源池：Node 提供实际运行机器；Pod 承载容器；Deployment 管理一组 Pod；Service 为匹配标签的一组 Pod 提供稳定入口。证据见 [验证记录](07-verification-record.md#src-07)。

## 关键对象

### Pod

**[Source-derived · SRC-08]** Pod 是最小可部署、可调度单元，可包含一个或多个紧密协作容器；这些容器共享网络和存储，并被调度到同一 Node。Pod 短暂，不适合保存持久数据。证据见 [验证记录](07-verification-record.md#src-08)。

### Deployment

**[Source-derived · SRC-09]** Deployment 管理无状态应用 Pod。用户声明副本数等期望状态，控制器维持实际状态，并支持滚动更新与回滚。证据见 [验证记录](07-verification-record.md#src-09)。

### Service

**[Source-derived · SRC-10]** Service 为一组 Pod 提供稳定虚拟 IP（ClusterIP）和 DNS 名称；它通过标签选择器关联后端 Pod，并把请求负载均衡到这些 Pod。证据见 [验证记录](07-verification-record.md#src-10)。

### Namespace 与 Node

**[Source-derived · SRC-11]** Namespace 逻辑隔离集群资源；Node 是实际承载 Pod 的工作机器。证据见 [验证记录](07-verification-record.md#src-11)。

## 协作例子

**[Source-derived example · SRC-12]** Deployment 创建带 `app: demo` 标签的 Pod；Service 以同一标签选择它们。因此 Pod 即使被替换，Service 仍能为匹配 Pod 保持访问入口。证据见 [验证记录](07-verification-record.md#src-12)。

下一节：[核心功能](03-core-functionality.md)。
