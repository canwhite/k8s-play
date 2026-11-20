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

### Service：稳定入口，不负责创建 Pod

常规无状态应用通常由 Deployment 创建并维护 Pod；Service 通过标签 selector 找到这些 Pod，为它们提供稳定的名称、虚拟 IP 和访问端口。Service 不“组合”或创建 Pod，也不直接引用 Deployment 名称。

```text
Deployment → 创建、替换、扩缩 Pod
Service    → 选择带匹配标签的 Pod，转发流量
```

请求端口路径通常如下：容器在 Pod 内监听 `containerPort`；Service 的 `targetPort` 指向该 Pod 端口；调用方访问 Service 的 `port`。

```text
客户端 → Service port:80 → targetPort:80 → Pod/containerPort:80
```

要让应用可访问，通常先创建 Service；但能否“对外”访问取决于 Service 类型：

| 目标 | 常用方式 | 访问范围 |
| --- | --- | --- |
| 集群内其他 Pod 访问 | `ClusterIP`（默认） | 仅集群内部，例如 `http://web` |
| 本地练习或简单演示 | `NodePort` | 通过 `<NodeIP>:<NodePort>` 访问 |
| 生产 HTTP/HTTPS | `ClusterIP` + Ingress 或 Gateway | 域名、TLS、路由等由 Ingress/Gateway 处理 |
| 云环境直接暴露网络入口 | `LoadBalancer` | 云控制器创建外部负载均衡器 |

因此，最简单的“应用跑起来并被集群内访问”是 Deployment + 默认 Service。最简单的“从集群外演示访问”通常是 `NodePort` Service：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  type: NodePort
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

应用后可通过 `<任一 Node IP>:30080` 访问。`nodePort` 可省略，让 Kubernetes 自动分配；这里固定为 `30080` 只是为了让练习访问地址明确。生产 Web 服务通常不直接大量使用 NodePort，而是在 Service 前放 Ingress 或 Gateway。

### 生产公网入口：Gateway + HTTPRoute

生产 Web 应用通常保持业务 Service 为 `ClusterIP`，再由 Gateway 统一处理公网流量。Gateway 监听外部端口并处理 TLS；HTTPRoute 声明域名、路径应转发到哪个 Service；Service 最后再按标签选择 Pod。

```text
互联网用户
  ↓ https://app.example.com
外部负载均衡器 / Gateway Controller
  ↓
Gateway：监听 443，终止 TLS
  ↓ 按域名、路径匹配
HTTPRoute
  ↓
ClusterIP Service：web:80
  ↓
web Pods
```

职责分工：

```text
Deployment：创建、更新、扩缩 web Pods
Service：稳定集群内地址，选择 app=web 的 Pods
Gateway：公网监听 443，配置 TLS 入口
HTTPRoute：声明 app.example.com/ → web Service:80
```

先定义保持内部访问的 Service：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
  namespace: app
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
```

再定义 Gateway。`example-gateway-class` 是占位名称，必须替换为集群已安装 Gateway Controller 提供的实际 GatewayClass。

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: public-gateway
  namespace: app
spec:
  gatewayClassName: example-gateway-class
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      hostname: app.example.com
      tls:
        mode: Terminate
        certificateRefs:
          - kind: Secret
            name: app-example-com-tls
```

最后用 HTTPRoute 将域名与路径路由到内部 Service：

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
  namespace: app
spec:
  parentRefs:
    - name: public-gateway
  hostnames:
    - app.example.com
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: web
          port: 80
```

访问 `https://app.example.com/` 时，Gateway 接收并解密 HTTPS 请求，HTTPRoute 匹配域名和路径，将请求转发到 `app` Namespace 中的 `web` Service；Service 再将流量分发到带 `app=web` 标签的 Pod。

此示例依赖四项环境条件：集群已安装 Gateway API 与 Gateway Controller；存在正确的 `GatewayClass`；DNS 将 `app.example.com` 指向 Gateway 的公网地址；`app-example-com-tls` Secret 含有该域名可用的 TLS 证书和私钥。证书通常由 cert-manager 自动申请和续期。缺少其中任一项，YAML 本身不能让公网访问自动生效。

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

前文 Gateway 的 HTTPS 入口就是 Secret 的具体例子。`app-example-com-tls` 保存域名证书和私钥；Gateway 引用它后，在 443 端口完成 TLS 终止，再把解密后的 HTTP 请求按 HTTPRoute 转发到 `web` Service：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-example-com-tls
  namespace: app
type: kubernetes.io/tls
data:
  tls.crt: <Base64 编码的 TLS 证书>
  tls.key: <Base64 编码的 TLS 私钥>
```

`tls.crt` 是公开证书，`tls.key` 是必须保密的私钥，因此此证书材料必须使用 TLS Secret，而不能使用 ConfigMap。此 Secret 应与 Gateway 位于同一 Namespace；跨 Namespace 引用还需要 Gateway API 的额外授权。能修改该 Secret 或引用它的 Gateway 的主体都可能替换站点证书，应限制其写入权限。

## 输入与可观察结果

**[Source-derived · SRC-19]** 用户以 YAML 的 `spec` 描述期望状态。提交后，K8s 计算差异并使集群状态向 YAML 收敛。可用 `kubectl get`、`describe`、`logs` 观察结果。证据见 [验证记录](07-verification-record.md#src-19)。

**[Research-derived · RSC-05]** `get` 用于列出或读取资源；`describe` 给出特定资源的人类可读详细信息；`logs` 读取容器标准输出，`--previous` 可读取前一实例日志。它们回答不同问题，排查时不要只看其中一个。证据见 [验证记录](07-verification-record.md#rsc-05)。

以前文 `app` Namespace 中的 `web`、`public-gateway` 和 `web-route` 为例，可按对象层次观察：

```bash
# Deployment 是否创建足够且就绪的 web Pods
kubectl get deployment,pods -n app -l app=web
kubectl describe deployment web -n app

# Service 是否选择到端点
kubectl get service web -n app
kubectl get endpointslice -n app -l kubernetes.io/service-name=web

# Gateway 与路由是否被 Controller 接受并完成配置
kubectl get gateway public-gateway -n app
kubectl describe gateway public-gateway -n app
kubectl get httproute web-route -n app
kubectl describe httproute web-route -n app

# 应用启动失败或请求异常时，查看当前与前一实例日志
kubectl logs -n app -l app=web --all-containers=true
kubectl logs -n app <Pod 名称> --previous
```

`get deployment,pods` 应显示目标副本数与就绪 Pod；EndpointSlice 应包含 `web` Pod 的端点。Gateway 和 HTTPRoute 的 `describe` 输出应显示被 Controller 接受的条件与事件。`logs` 用来判断容器进程本身是否启动、报错或重启；它不能证明 Service、Gateway 或 HTTPRoute 已正确转发流量。Secret 不应用 `kubectl get secret ... -o yaml` 输出到终端或文档，因为输出含编码后的敏感数据；只需确认其名称、类型和 Gateway 引用一致。

## 边界

**[Source-derived · SRC-20]** Pod 的 IP 会随重启变化，且 Pod 短暂；因此稳定访问应交给 Service，持久数据不应依赖 Pod 本身。证据见 [验证记录](07-verification-record.md#src-20)。

K8s 可尝试恢复声明状态，但不会自动修复错误镜像、业务逻辑缺陷、缺失权限或错误标签。以前文 `app` Namespace 中的 `web` 为例：

| 声明或运行时问题 | 你会看到什么 | K8s 能做什么 | 仍需人处理什么 |
| --- | --- | --- | --- |
| `web` Pod 标签改成 `app: api`，Service selector 仍为 `app: web` | Service 存在，但对应 EndpointSlice 没有可用端点；Gateway 最终无法转发到后端 | 保持 Service 与 Pod 存在 | 统一 Service selector 和 Pod 模板标签 |
| 镜像写成不存在的 `nginx:9.99` | Pod 反复处于 `ImagePullBackOff`；`kubectl describe pod` 的 Events 显示拉取失败 | 持续尝试创建、拉取 Pod | 改正镜像名、tag 或镜像仓库凭据 |
| 容器进程启动后立即退出 | Pod 可能反复重启并显示 `CrashLoopBackOff`；`kubectl logs --previous` 显示前次退出原因 | 按 Deployment 的期望副本数重建 Pod | 修复应用配置、启动命令或业务代码 |
| Gateway 引用的 `app-example-com-tls` Secret 缺失或无效 | Gateway 的 `describe` Conditions 或 Events 显示引用/证书错误；HTTPS 入口不可用 | 保留 Gateway 声明并等待依赖满足 | 创建正确的 TLS Secret，修正名称、Namespace 或证书内容 |

也就是说，控制器负责让实际对象尽量接近 YAML；它不知道“镜像 tag 拼错”“标签语义不一致”或“程序为何退出”。排查顺序可从 `kubectl get pods -n app` 确认状态开始，再用 `kubectl describe pod <Pod 名称> -n app` 看 Events、用 `kubectl logs -n app <Pod 名称> --previous` 看前次容器输出；若 Pod 已就绪但访问失败，再检查 Service 的 EndpointSlice、Gateway 与 HTTPRoute 的 Conditions。

## 具体例子

**[Source-derived example · SRC-21]** YAML 将镜像从 `nginx:1.25` 改为 `nginx:1.26` 后再次 `kubectl apply -f demo.yaml`，可触发滚动更新。证据见 [验证记录](07-verification-record.md#src-21)。

**[Research-derived check · RSC-03]** 预期观察命令：

```bash
kubectl rollout status deployment/demo-deploy
kubectl get deployment demo-deploy
```

在可访问集群中，前者等待 rollout 完成；后者显示 Deployment 当前状态。此处是官方命令语义，不是本仓库的实测集群输出。

下一节：[使用方法](04-usage.md)。
