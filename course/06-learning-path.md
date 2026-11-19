# 06. 学习路径

## 学习目标

按依赖顺序完成从概念理解到 YAML 操作和状态观察的入门练习；每一步均有输入、命令、可观察完成条件和下一步。

## 课程闭环

```text
为什么需要 K8s
  → Pod / Deployment / Service 怎样协作
  → 声明能触发哪些能力、有哪些边界
  → 用 YAML 和 kubectl 观察完整生命周期
  → 选择练习环境与管理工具
  → 独立完成一次部署、更新、排查、清理
```

不要跳过前置概念直接复制命令。命令能执行不等于理解了资源关系；理解对象关系后，`get`、`describe`、`logs` 的输出才有可解释的含义。

## 路径

### 阶段 0：准备边界与环境

1. **[Research-derived · RSC-08]** 安装 `kubectl`，并准备自己有权使用的集群；没有集群时，按官方 kind 或 minikube 入门文档建立本地练习环境。证据见 [验证记录](07-verification-record.md#rsc-08)。
2. 执行 `kubectl config current-context` 与 `kubectl get nodes`。前者确认目标 context，后者确认 API 可访问和工作节点可见。

**完成条件：** 能明确说出你将在哪个集群练习；若无授权或命令失败，停在本阶段排查凭据、context 或本地环境，不向未知集群提交 YAML。

### 阶段 1：建立声明式心智模型

1. **[Source-derived · SRC-32]** 理解 K8s 将多台机器抽象为资源池，并通过声明式配置和自动化控制维持期望状态。证据见 [验证记录](07-verification-record.md#src-32)。
2. 将“我要三个固定容器”改写成“我要持续维持三个满足模板的 Pod 副本”。
3. 阅读 [定位与背景](01-positioning-and-background.md) 的控制环图，说明实际 Pod 可替换，期望副本数不变。

**完成条件：** 能解释为何 `apply` 后还需要观察资源状态，而非把提交成功等同于应用已可用。

### 阶段 2：掌握三个对象和标签关系

1. **[Source-derived · SRC-33]** 学习 Pod、Deployment、Service；理解 Deployment 管理 Pod，Service 通过标签选择器提供稳定入口。证据见 [验证记录](07-verification-record.md#src-33)。
2. 在示例 YAML 中圈出 `spec.replicas`、`template.metadata.labels`、`Service.spec.selector`。
3. **[Research-derived · RSC-02]** 说明 Pod 共享网络与存储、且相对短暂；因此客户不应依赖单个 Pod IP。证据见 [验证记录](07-verification-record.md#rsc-02)。

**完成条件：** 能解释 `app: demo` 若只出现在 Service selector 而不在 Pod 模板标签中，会导致什么：Service 没有自动匹配的后端。

### 阶段 3：读懂并提交声明文件

1. **[Source-derived · SRC-34]** 学习 YAML 中的 `spec` 如何表达 Deployment、Service 的期望状态。证据见 [验证记录](07-verification-record.md#src-34)。
2. 按 [使用方法](04-usage.md) 创建 `demo.yaml`，在自己的练习环境运行：

```bash
kubectl apply -f demo.yaml
kubectl get deployments,pods,services
kubectl rollout status deployment/demo-deploy
```

3. **[Research-derived · RSC-05]** 用 `get` 看资源集合和状态；用 `rollout status` 等待 Deployment 发布状态。证据见 [验证记录](07-verification-record.md#rsc-05)。

**完成条件：** Deployment 可用副本达到目标；Service 已创建；你能区分“YAML 已提交”与“Deployment rollout 已完成”。集群特定字段与耗时以实际输出为准。

### 阶段 4：变更与观察

1. **[Source-derived · SRC-35]** 对 `demo.yaml` 执行 apply，再用 get、describe、logs 观察资源和排查信息。证据见 [验证记录](07-verification-record.md#src-35)。
2. 将镜像由 `nginx:1.25` 改为 `nginx:1.26`，执行：

```bash
kubectl apply -f demo.yaml
kubectl rollout status deployment/demo-deploy
```

3. **[Source-derived · SRC-36]** 该变更练习用于观察滚动更新。证据见 [验证记录](07-verification-record.md#src-36)。**[Research-derived · RSC-03]** 镜像位于 Pod 模板，修改会触发 rollout；只改变副本数不会触发 rollout。证据见 [验证记录](07-verification-record.md#rsc-03)。

**完成条件：** 能说明“模板变更”和“副本数变更”的区别，并在 `rollout status` 完成后继续下一步。

### 阶段 5：故障观察与清理

1. 任选一个 Pod，执行：

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

2. **[Source-derived · SRC-37]** 使用 `kubectl get pods`、`kubectl get deployments`、`kubectl get services` 分别查看三类资源。证据见 [验证记录](07-verification-record.md#src-37)。
3. **[Source-derived · SRC-38]** 说明 Pod IP 变化时仍需 Service 作为固定入口。证据见 [验证记录](07-verification-record.md#src-38)。
4. 清理：`kubectl delete -f demo.yaml`，再运行 `kubectl get deployments,pods,services` 确认资源已被移除。

**完成条件：** 能按“状态 → 事件 → 日志”说出排查顺序，且未在练习集群遗留本课程创建的资源。

## 总结验收

- **[Source-derived example · SRC-39]** 能指出 `demo.yaml` 中 `app: demo` 同时出现在 Pod 模板标签和 Service 选择器，因而构成关联。证据见 [验证记录](07-verification-record.md#src-39)。
- 能解释 Deployment、Pod、Service 各自职责，并在 YAML 中定位对应字段。
- 能完成 apply、观察、镜像更新、rollout 观察、describe/logs 排查、delete 的一条闭环。
- 能说出课程的边界：没有集群访问、正确权限和有效镜像时，命令不会自动成功；K8s 也不会替你修复应用逻辑错误。

课程复盘：[整体 Post-Mortem](08-course-post-mortem.md)。

下一节：[验证记录](07-verification-record.md)。
