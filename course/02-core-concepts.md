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

可以把 Deployment 理解为无状态应用 Pod 的“管家”：你声明最终要几个、跑哪个版本，它持续让集群实际状态接近期望状态。

```text
期望：3 个 Pod，实际：2 个 Pod  → Deployment 补建 1 个 Pod
期望：3 个 Pod，实际：4 个 Pod  → Deployment 删除多余 Pod

Deployment → ReplicaSet → Pods
```

无状态应用通常不把重要数据保存在 Pod 本地，例如网站后端、API 服务或 Nginx。某个 Pod 被替换后，新 Pod 仍可继续处理请求。

修改 `.spec.replicas`，例如从 `3` 改为 `5`，是在扩缩容：运行同一版本的 Pod 数量改变。修改 `.spec.template`，例如镜像从 `v1` 改为 `v2`，才是发布新版本：Deployment 会逐步用新 Pod 替换旧 Pod；出现问题时可回滚。

Deployment 的本体是 Kubernetes API 对象。通常用 YAML 声明并保存到 Git；命令行适合快速练习、查看或临时调整。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.27
```

```bash
kubectl apply -f deployment.yaml
kubectl create deployment web --image=nginx:1.27
kubectl scale deployment web --replicas=3
kubectl get deployment
kubectl describe deployment web
```

### Service：稳定访问契约

**[Source-derived · SRC-10]** Service 为一组 Pod 提供稳定虚拟 IP（ClusterIP）和 DNS 名称；它通过标签选择器关联后端 Pod，并把请求负载均衡到这些 Pod。证据见 [验证记录](07-verification-record.md#src-10)。

**[Research-derived · RSC-04]** Service 是一组网络端点的稳定抽象。带 `.spec.selector` 的 Service 会持续发现匹配 Pod 并更新 EndpointSlice；同一 Namespace 中客户端通常可使用 Service 名称访问。证据见 [验证记录](07-verification-record.md#rsc-04)。

Service 也是 Kubernetes API 对象。它用 selector 找到匹配标签的 Pod，为调用方提供固定名称和虚拟 IP；Pod 被替换、IP 改变时，调用方仍可访问 Service 名称。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
```

上例中 `port` 是 Service 提供的端口，`targetPort` 是请求转发到 Pod 的端口。它选择前面 Deployment 创建、带有 `app: web` 标签的 Pod；集群内客户端通常访问 `http://web`，不直接依赖某个 Pod IP。

```bash
kubectl apply -f service.yaml
kubectl expose deployment web --port=80 --target-port=80
kubectl get service
kubectl describe service web
```

### Namespace 与 Node

**[Source-derived · SRC-11]** Namespace 逻辑隔离集群资源；Node 是实际承载 Pod 的工作机器。证据见 [验证记录](07-verification-record.md#src-11)。

Namespace 是 Kubernetes API 对象，用于给资源分组。同一个集群中可在不同 Namespace 分别创建同名的 `web` Service 或 Deployment。它不是物理机器分区，也不是安全边界的全部实现；本课程先把它理解为“资源文件夹”。命令未指定 Namespace 时使用当前上下文的默认 Namespace。

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: learning
```

```bash
kubectl apply -f namespace.yaml
kubectl create namespace learning
kubectl get namespace
kubectl get pod -n learning
kubectl apply -f deployment.yaml -n learning
```

`-n learning` 表示在 `learning` Namespace 中操作资源。要让 YAML 固定创建到某个 Namespace，可在对象的 `metadata` 下添加 `namespace: learning`。

Node 是承载 Pod 的实际机器，可以是物理机或虚拟机。它也是 Kubernetes API 对象，但通常由集群安装流程、云平台或运维人员注册，不会像 Deployment 一样由应用开发者用 YAML 从零创建。调度器会根据资源需求等条件，将 Pod 安排到合适的 Node。

```bash
kubectl get node
kubectl describe node <node-name>
kubectl get pod -o wide
```

`kubectl get pod -o wide` 会额外显示每个 Pod 所在的 Node。关系如下：

```text
Namespace：资源分组
    └─ Deployment、Service、Pod 等资源

Node：实际运行机器
    └─ Pod 在某个 Node 上运行
```

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
