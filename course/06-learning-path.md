# 06. 学习路径

## 学习目标

按依赖顺序完成从概念理解到 YAML 操作和状态观察的入门练习。

## 路径

1. **[Source-derived · SRC-32] 前提概念：** 先理解 K8s 将多台机器抽象为资源池，并通过声明式配置和自动化控制维持期望状态。证据见 [验证记录](07-verification-record.md#src-32)。
2. **[Source-derived · SRC-33] Lesson 1：** 学习 Pod、Deployment、Service；理解 Deployment 管理 Pod，Service 通过标签选择器提供稳定入口。证据见 [验证记录](07-verification-record.md#src-33)。
3. **[Source-derived · SRC-34] Lesson 2：** 学习 YAML 中的 `spec` 如何表达 Deployment、Service 的期望状态。证据见 [验证记录](07-verification-record.md#src-34)。
4. **[Source-derived · SRC-35] 练习：** 对 `demo.yaml` 执行 apply，再用 get、describe、logs 观察资源和排查信息。证据见 [验证记录](07-verification-record.md#src-35)。
5. **[Source-derived · SRC-36] 变更练习：** 将镜像版本从 `nginx:1.25` 改为 `nginx:1.26`，再次 apply，观察滚动更新这一源材料声明的结果。证据见 [验证记录](07-verification-record.md#src-36)。

## 可观察完成检查

- **[Source-derived · SRC-37]** 能用 `kubectl get pods`、`kubectl get deployments`、`kubectl get services` 分别查看三类资源。证据见 [验证记录](07-verification-record.md#src-37)。
- **[Source-derived · SRC-38]** 能说明为何 Pod IP 变化时仍需 Service 作为固定入口。证据见 [验证记录](07-verification-record.md#src-38)。
- **[Source-derived example · SRC-39]** 能指出 `demo.yaml` 中 `app: demo` 同时出现在 Pod 模板标签和 Service 选择器，因而构成关联。证据见 [验证记录](07-verification-record.md#src-39)。

下一节：[验证记录](07-verification-record.md)。
