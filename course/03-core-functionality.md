# 03. 核心功能

## 学习目标

能把 K8s 自动化能力对应到输入、控制器动作、可观察结果和边界；能区分“系统可调节”与“应用天然正确”。

## 自动化能力

- **[Source-derived · SRC-13]** 服务发现与负载均衡：为 Pod 提供 IP 和 DNS，并在 Pod 间分发流量。证据见 [验证记录](07-verification-record.md#src-13)。
- **[Source-derived · SRC-14]** 上线与回滚：受控更新应用，并可在出现问题时回滚。证据见 [验证记录](07-verification-record.md#src-14)。
- **[Source-derived · SRC-15]** 自我修复：重启失败容器、替换无响应 Pod，未就绪前不导入流量。证据见 [验证记录](07-verification-record.md#src-15)。
- **[Source-derived · SRC-16]** 水平扩缩：按 CPU 使用率等指标自动或手动调整 Pod 副本数。证据见 [验证记录](07-verification-record.md#src-16)。
- **[Source-derived · SRC-17]** 存储编排、配置与密钥管理：挂载存储；无需重建镜像即可更新配置，并管理密码、令牌等敏感信息。证据见 [验证记录](07-verification-record.md#src-17)。
- **[Source-derived · SRC-18]** 调度与批处理：按资源需求把容器调度到合适 Node；也能管理 Job、CronJob。证据见 [验证记录](07-verification-record.md#src-18)。

## 功能如何变成可观察结果

| 你声明的输入 | K8s 的对象/动作 | 观察什么 | 不能据此假定什么 |
| --- | --- | --- | --- |
| `replicas: 3` | Deployment 维持目标 Pod 数 | `kubectl get deployment` 的 READY、AVAILABLE | 单个 Pod 名称或 IP 永远不变 |
| 修改 Pod 模板镜像 | Deployment 创建新 ReplicaSet、执行 rollout | `kubectl rollout status deployment/demo` | 扩缩本身会产生新发布版本 |
| Service selector | Service 发现匹配 Pod 端点 | Service 与端点是否存在 | 任意标签的 Pod 都会收到流量 |
| ConfigMap/Secret 引用 | Pod 注入配置或敏感数据 | Pod spec 引用与应用行为 | ConfigMap 适合保存秘密 |

**[Research-derived · RSC-03]** Deployment 更新会在 Pod 模板变化时触发；滚动更新期间新 ReplicaSet 扩容、旧 ReplicaSet 缩容。`kubectl rollout status` 可等待和观察发布状态。证据见 [验证记录](07-verification-record.md#rsc-03)。

## 配置与 Secret 的边界

**[Research-derived · RSC-06]** ConfigMap 存储非机密键值配置，可让环境配置与容器镜像解耦；Pod 可把它作为环境变量、命令参数或卷文件使用。ConfigMap 数据作为环境变量使用时，更新通常需要重启 Pod 才会被应用读取。证据见 [验证记录](07-verification-record.md#rsc-06)。

**[Research-derived · RSC-07]** Secret 面向密码、令牌、密钥等机密数据，但 Secret 并不自动等于安全：默认数据存储未必加密，能创建 Pod 的权限也可能间接带来读取同 Namespace Secret 的能力。生产环境需要静态加密、最小权限 RBAC，并只把 Secret 暴露给需要它的容器。证据见 [验证记录](07-verification-record.md#rsc-07)。

## 输入与可观察结果

**[Source-derived · SRC-19]** 用户以 YAML 的 `spec` 描述期望状态。提交后，K8s 计算差异并使集群状态向 YAML 收敛。可用 `kubectl get`、`describe`、`logs` 观察结果。证据见 [验证记录](07-verification-record.md#src-19)。

**[Research-derived · RSC-05]** `get` 用于列出或读取资源；`describe` 给出特定资源的人类可读详细信息；`logs` 读取容器标准输出，`--previous` 可读取前一实例日志。它们回答不同问题，排查时不要只看其中一个。证据见 [验证记录](07-verification-record.md#rsc-05)。

## 边界

**[Source-derived · SRC-20]** Pod 的 IP 会随重启变化，且 Pod 短暂；因此稳定访问应交给 Service，持久数据不应依赖 Pod 本身。证据见 [验证记录](07-verification-record.md#src-20)。

K8s 可尝试恢复声明状态，但不会自动修复错误镜像、业务逻辑缺陷、缺失权限或错误标签。观察到 Pod 未就绪时，应从事件、容器日志、镜像与 YAML 标签对应关系逐层定位。

## 具体例子

**[Source-derived example · SRC-21]** YAML 将镜像从 `nginx:1.25` 改为 `nginx:1.26` 后再次 `kubectl apply -f demo.yaml`，可触发滚动更新。证据见 [验证记录](07-verification-record.md#src-21)。

**[Research-derived check · RSC-03]** 预期观察命令：

```bash
kubectl rollout status deployment/demo-deploy
kubectl get deployment demo-deploy
```

在可访问集群中，前者等待 rollout 完成；后者显示 Deployment 当前状态。此处是官方命令语义，不是本仓库的实测集群输出。

下一节：[使用方法](04-usage.md)。
