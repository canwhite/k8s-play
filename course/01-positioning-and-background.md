# 01. 定位与背景

## 学习目标

能说明 Kubernetes（K8s）解决的问题、适用场景，以及学习本课程后的可观察结果。

## K8s 是什么

**[Source-derived · SRC-01]** Kubernetes 是容器编排平台，面向规模化场景中自动、可靠地管理大量容器的需求。它将多台机器抽象为统一资源池，再借助声明式配置与自动化控制维持期望状态。证据见 [验证记录](07-verification-record.md#src-01)。

**[Source-derived · SRC-02]** K8s 的目标是减少手工运维，使人员更多聚焦应用；它保持开放、可扩展，不限定应用类型或技术栈。证据见 [验证记录](07-verification-record.md#src-02)。

## 面向谁

**[Source-derived · SRC-03]** 本课程适合刚接触 K8s、需要先理解 Pod、Deployment、Service 协作关系的学习者。源材料明确建议入门先聚焦这三个对象。证据见 [验证记录](07-verification-record.md#src-03)。

## 完成后能做到什么

- **[Source-derived · SRC-04]** 解释声明式 API 与控制器模式：用户描述目标状态，控制循环持续让实际状态收敛。证据见 [验证记录](07-verification-record.md#src-04)。
- **[Source-derived · SRC-05]** 读懂一个包含 Deployment 和 Service 的基本 YAML，并用 `kubectl apply -f` 提交。证据见 [验证记录](07-verification-record.md#src-05)。

## 具体例子

**[Source-derived example · SRC-06]** 期望运行 3 个 nginx 副本时，声明可由 Deployment 表达；Pod 副本减少时，控制器会补齐。证据见 [验证记录](07-verification-record.md#src-06)。

下一节：[核心概念](02-core-concepts.md)。
