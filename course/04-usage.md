# 04. 使用方法

## 学习目标

能在有权限的练习集群中，按“检查前提 → 声明资源 → 提交 → 观察 → 更新 → 排查 → 清理”部署第 03 章的完整 `web` 示例：Deployment、Service、Gateway、HTTPRoute 与 TLS Secret 共同构成公网 HTTPS 入口，并知道每层应观察什么。

## 前提

**[Source-derived · SRC-22]** 源材料将 `kubectl` 与 YAML 声明文件列为管理集群的主流方式，并给出基础语法：`kubectl [command] [TYPE] [NAME] [flags]`。证据见 [验证记录](07-verification-record.md#src-22)。

**[Research-derived · RSC-08]** `kubectl` 是向 Kubernetes 集群执行命令的 CLI。开始前需要安装它，并拥有可访问的集群和合适凭据；官方学习环境页、kind、minikube 都是建立练习环境的入口。证据见 [验证记录](07-verification-record.md#rsc-08)。

本课程不替你创建集群，也不假定当前仓库可连任何集群。以下命令会修改目标集群；仅在自己的练习 Namespace 或得到授权的环境中执行。

本练习分为两层：Deployment、Service、EndpointSlice 与 rollout 可在普通 Kubernetes 练习集群验证；Gateway 的公网 HTTPS 入口还需要已安装 Gateway API 和 Gateway Controller、实际可用的 GatewayClass、将 `app.example.com` 指向 Gateway 的 DNS，以及该域名的有效 TLS 证书。

在继续前，创建或选择自己的练习 Namespace（下文使用 `app`）。准备好 `app.example.com` 的证书文件与私钥文件后，创建 Gateway 要引用的 TLS Secret：

```bash
kubectl create namespace app
kubectl create secret tls app-example-com-tls \
  --namespace app \
  --cert=/安全位置/app.example.com.crt \
  --key=/安全位置/app.example.com.key
```

该命令创建类型为 `kubernetes.io/tls` 的 Secret，包含该域名的 `tls.crt` 与 `tls.key`。若 `app` Namespace 已存在，第一条命令会报错，可跳过；若要替换已存在的 Secret，先确认它不被其他工作负载使用，再按证书来源的轮换流程更新。不要把私钥、Base64 编码内容、证书/私钥文件或 Secret YAML 提交到仓库、终端记录或课程文档。

## 练习清单

创建 `web-gateway.yaml`。它复用第 03 章完整对象链：`web` Deployment 创建带 `app: web` 标签的 Pod；Service 选择这些 Pod；Gateway 在 443 终止 TLS；HTTPRoute 将 `app.example.com/` 转发到 `web:80`。将 `example-gateway-class` 替换为集群中实际存在的 GatewayClass。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: app
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
          image: nginx:1.25
          ports:
            - containerPort: 80
---
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
---
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
---
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

**[Source-derived · SRC-24]** Deployment 与 Service 的结构来自源材料的声明示例。证据见 [验证记录](07-verification-record.md#src-24)。Gateway、HTTPRoute 与 TLS Secret 的职责见 [核心功能](03-core-functionality.md#生产公网入口gateway--httproute)。

## 操作顺序

### 1. 先看自己将操作的环境

```bash
kubectl config current-context
kubectl get nodes
kubectl get gatewayclass
```

**[Illustrative safety check]** 先确认当前 context、Node 列表与可用 GatewayClass，避免把练习 YAML 提交到错误集群。若无权限或没有集群，不要继续；回到前提准备练习环境。

### 2. 提交声明

确认 `web-gateway.yaml` 中的 GatewayClass 已替换为实际名称，且 `app-example-com-tls` 已存在后，执行：

```bash
kubectl apply -f web-gateway.yaml
```

**[Source-derived · SRC-23]** 编写 YAML，定义期望状态，然后以 `kubectl apply -f` 提交。证据见 [验证记录](07-verification-record.md#src-23)。

**[Research-derived · RSC-05]** `apply -f` 通过资源定义文件创建或更新对象；它是官方推荐的声明式应用管理方式。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 3. 分层观察是否收敛

```bash
# 工作负载与稳定入口
kubectl rollout status deployment/web -n app
kubectl get deployment,pods -n app -l app=web
kubectl get service web -n app
kubectl get endpointslice -n app -l kubernetes.io/service-name=web

# Gateway 与路由对象是否被 Controller 接受
kubectl describe gateway public-gateway -n app
kubectl describe httproute web-route -n app
```

预期观察：Deployment 的 READY/AVAILABLE 达到目标副本数；EndpointSlice 包含 `web` Pod 的可用端点；Gateway 与 HTTPRoute 的 `describe` 显示 Controller 接受情况和相关 Events。具体 Pod 名称、IP、拉取速度、Gateway 地址及完成时间取决于你的集群，不能从本文推断。

Gateway/HTTPRoute 被接受不等于 DNS、证书和公网 HTTPS 已经可用。只有前提中的 DNS 与 TLS 证书均正确时，才能再从集群外访问 `https://app.example.com/` 验证完整链路。

**[Source-derived · SRC-25]** 可用 `get` 查看 Pod、Deployment、Service。证据见 [验证记录](07-verification-record.md#src-25)。**[Research-derived · RSC-05]** `rollout status` 用于等待或观察 Deployment 滚动发布状态。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 4. 更新镜像并观察 rollout

将 YAML 中 `nginx:1.25` 改为 `nginx:1.26`，再执行：

```bash
kubectl apply -f web-gateway.yaml
kubectl rollout status deployment/web -n app
kubectl get pods -n app -l app=web
```

Service、Gateway 和 HTTPRoute 不需要因这次镜像更新而改动；它们继续按 `app: web` 标签与 `web:80` 后端引用新的就绪 Pod。

**[Source-derived · SRC-26]** 修改 YAML 后再次 apply 会更新资源；源材料以镜像版本更新说明这一流程。证据见 [验证记录](07-verification-record.md#src-26)。**[Research-derived · RSC-03]** 镜像位于 Pod 模板，修改它会触发 Deployment rollout。证据见 [验证记录](07-verification-record.md#rsc-03)。

### 5. 排查未就绪 Pod 或不可访问入口

```bash
# Pod 未就绪或重启
kubectl get pods -n app -l app=web
kubectl describe pod <Pod 名称> -n app
kubectl logs -n app <Pod 名称> --previous

# Pod 已就绪但访问失败
kubectl get endpointslice -n app -l kubernetes.io/service-name=web
kubectl describe gateway public-gateway -n app
kubectl describe httproute web-route -n app
```

先用 `get` 确认对象及状态，再用 `describe` 查看 Events 或 Conditions，最后读取容器日志。多容器 Pod 需要加 `-c <容器名称>`；刚重启后的容器可使用 `--previous` 读取前一实例日志。Pod 日志正常只能说明应用进程可能正常，不能证明 Service、Gateway、HTTPRoute、DNS 或证书链路全部正确。

**[Source-derived · SRC-25]** `describe` 用于查看 Pod 详细事件，`logs` 用于查看容器日志。证据见 [验证记录](07-verification-record.md#src-25)。**[Research-derived · RSC-05]** `logs` 支持 `-c`、`--previous` 等定位选项。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 6. 清理练习资源

```bash
kubectl delete -f web-gateway.yaml
```

该命令只删除清单中的 Deployment、Service、Gateway 与 HTTPRoute，不删除练习 Namespace 或 TLS Secret。若不再需要 `app-example-com-tls`，请按证书来源和保留策略单独处理，避免删除其他工作负载使用的证书。

**[Source-derived · SRC-26]** `kubectl delete -f` 删除由该文件定义的资源。证据见 [验证记录](07-verification-record.md#src-26)。执行后可运行 `kubectl get deployment,service,gateway,httproute -n app`，确认练习对象不再存在。

## 完成检查

完成本章时，应能回答：

- `apply` 后为什么不能只盯住某个 Pod 名称？
- `Service` 存在时，为什么还要检查 EndpointSlice？
- `get`、`describe`、`logs` 分别回答什么问题？
- 为什么更新镜像后要看 `rollout status`，但不必改动 Gateway 与 HTTPRoute？
- 为什么 Gateway 被 Controller 接受，仍不能证明公网 HTTPS 已通？
- 为什么不应把 TLS Secret 的内容写入练习清单？

下一节：[相关工具](05-related-tools.md)。
