# 03. 核心功能

## 学习目标

能将 K8s 自动化能力与其输入、可观察结果和限制对应起来。

## 自动化能力

- **[Source-derived · SRC-13]** 服务发现与负载均衡：为 Pod 提供 IP 和 DNS，并在 Pod 间分发流量。证据见 [验证记录](07-verification-record.md#src-13)。
- **[Source-derived · SRC-14]** 上线与回滚：受控更新应用，并可在出现问题时回滚。证据见 [验证记录](07-verification-record.md#src-14)。
- **[Source-derived · SRC-15]** 自我修复：重启失败容器、替换无响应 Pod，未就绪前不导入流量。证据见 [验证记录](07-verification-record.md#src-15)。
- **[Source-derived · SRC-16]** 水平扩缩：按 CPU 使用率等指标自动或手动调整 Pod 副本数。证据见 [验证记录](07-verification-record.md#src-16)。
- **[Source-derived · SRC-17]** 存储编排、配置与密钥管理：挂载本地、云盘或网络存储；无需重建镜像即可更新配置，并管理密码、令牌等敏感信息。证据见 [验证记录](07-verification-record.md#src-17)。
- **[Source-derived · SRC-18]** 调度与批处理：按资源需求把容器调度到合适 Node；也能管理 Job、CronJob。证据见 [验证记录](07-verification-record.md#src-18)。

## 输入与可观察结果

**[Source-derived · SRC-19]** 用户以 YAML 的 `spec` 描述期望状态。提交后，K8s 计算差异并使集群状态向 YAML 收敛。可用 `kubectl get`、`describe`、`logs` 观察结果。证据见 [验证记录](07-verification-record.md#src-19)。

## 边界

**[Source-derived · SRC-20]** Pod 的 IP 会随重启变化，且 Pod 短暂；因此稳定访问应交给 Service，持久数据不应依赖 Pod 本身。证据见 [验证记录](07-verification-record.md#src-20)。

## 具体例子

**[Source-derived example · SRC-21]** YAML 将镜像从 `nginx:1.25` 改为 `nginx:1.26` 后再次 `kubectl apply -f demo.yaml`，可触发滚动更新。证据见 [验证记录](07-verification-record.md#src-21)。

下一节：[使用方法](04-usage.md)。
