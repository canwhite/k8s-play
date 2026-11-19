# 02. 核心概念

## 学习目标

能区分 Cluster、Node、Namespace、Pod、Deployment、Service；能按“Deployment → Pod → Service”解释一次请求与一次替换的关系。

## 心智模型

**[Source-derived · SRC-07]** 可把集群理解为统一资源池：Node 提供实际运行机器；Pod 承载容器；Deployment 管理一组 Pod；Service 为匹配标签的一组 Pod 提供稳定入口。证据见 [验证记录](07-verification-record.md#src-07)。

**[Research-derived · RSC-01]** 集群由可承载工作负载的 Node 组成；Kubernetes 根据资源需求将工作负载放置到 Node。控制平面组件维持集群目标状态。本课程不展开控制平面组件配置，只使用它解释“声明为何会被持续执行”。证据见 [验证记录](07-verification-record.md#rsc-01)。

## 关键对象

### Pod：容器的共同运行单元

**[Source-derived · SRC-08]** Pod 是最小可部署、可调度单元，可包含一个或多个紧密协作容器；这些容器共享网络和存储，并被调度到同一 Node。Pod 短暂，不适合保存持久数据。证据见 [验证记录](07-verification-record.md#src-08)。

**[Research-derived · RSC-02]** Pod 内的容器共享网络命名空间、Pod IP 和端口空间，可通过 `localhost` 通信，也可共享挂载卷。Pod 是相对短暂、可替换的实体；需要复制、更新或自愈时，通常让工作负载控制器管理 Pod。证据见 [验证记录](07-verification-record.md#rsc-02)。

不要把 Pod 等同于“永远不变的服务器”。应用状态需要持久化时，应选择适合的存储方案；本入门课只强调：不能把 Pod IP 或本地临时文件当作稳定接口。

### Deployment：无状态应用的期望状态管理者

**[Source-derived · SRC-09]** Deployment 管理无状态应用 Pod。用户声明副本数等期望状态，控制器维持实际状态，并支持滚动更新与回滚。证据见 [验证记录](07-verification-record.md#src-09)。

**[Research-derived · RSC-03]** `.spec.replicas` 表示期望 Pod 数量；Deployment 会管理 ReplicaSet，以维持该数量。只有 Pod 模板 `.spec.template` 变化才会触发 rollout；只改副本数是扩缩，不是发布。证据见 [验证记录](07-verification-record.md#rsc-03)。

### Service：稳定访问契约

**[Source-derived · SRC-10]** Service 为一组 Pod 提供稳定虚拟 IP（ClusterIP）和 DNS 名称；它通过标签选择器关联后端 Pod，并把请求负载均衡到这些 Pod。证据见 [验证记录](07-verification-record.md#src-10)。

**[Research-derived · RSC-04]** Service 是一组网络端点的稳定抽象。带 `.spec.selector` 的 Service 会持续发现匹配 Pod 并更新 EndpointSlice；同一 Namespace 中客户端通常可使用 Service 名称访问。证据见 [验证记录](07-verification-record.md#rsc-04)。

### Namespace 与 Node

**[Source-derived · SRC-11]** Namespace 逻辑隔离集群资源；Node 是实际承载 Pod 的工作机器。证据见 [验证记录](07-verification-record.md#src-11)。

Namespace 不是安全边界的全部实现，也不是物理机器分区；本课程中它首先用于避免不同练习或团队的同名资源混在一起。命令未指定 Namespace 时使用当前上下文的默认 Namespace。

## 标签把对象连起来

标签是附在对象上的键值对。Deployment 的 Pod 模板打标签；Service 的 selector 选择同一组标签。标签不匹配时，Service 不会自动把流量送到该 Pod。

```text
Deployment template labels: app: demo
            ↓ 创建
Pods: app: demo ──── Service selector: app: demo
            ↑                         ↓
       可替换 Pod                 稳定访问入口
```

**[Source-derived example · SRC-12]** Deployment 创建带 `app: demo` 标签的 Pod；Service 以同一标签选择它们。因此 Pod 即使被替换，Service 仍能为匹配 Pod 保持访问入口。证据见 [验证记录](07-verification-record.md#src-12)。

## 小练习

给以下三件事选对象：

1. “应用要有两个副本” → Deployment。
2. “两个紧密协作容器共享网络运行” → Pod。
3. “调用方不应依赖会变的 Pod IP” → Service。

若能说明三者为何不是同一个对象，再进入下一章。

下一节：[核心功能](03-core-functionality.md)。
