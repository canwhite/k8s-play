# 04. 使用方法

## 学习目标

能在有权限的练习集群中，按“检查前提 → 声明资源 → 提交 → 观察 → 更新 → 排查 → 清理”完成一个 Deployment 与 Service 练习，并知道每步应观察什么。

## 前提

**[Source-derived · SRC-22]** 源材料将 `kubectl` 与 YAML 声明文件列为管理集群的主流方式，并给出基础语法：`kubectl [command] [TYPE] [NAME] [flags]`。证据见 [验证记录](07-verification-record.md#src-22)。

**[Research-derived · RSC-08]** `kubectl` 是向 Kubernetes 集群执行命令的 CLI。开始前需要安装它，并拥有可访问的集群和合适凭据；官方学习环境页、kind、minikube 都是建立练习环境的入口。证据见 [验证记录](07-verification-record.md#rsc-08)。

本课程不替你创建集群，也不假定当前仓库可连任何集群。以下命令会修改目标集群；仅在自己的练习 Namespace 或得到授权的环境中执行。

## 练习清单

创建 `demo.yaml`。这个示例把 `app: demo` 同时放入 Deployment Pod 模板和 Service selector，建立稳定入口与可替换后端的关联。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
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
  name: demo-service
spec:
  selector:
    app: demo
  ports:
    - port: 80
      targetPort: 80
```

**[Source-derived · SRC-24]** 上述结构来自源材料的 Deployment 与 Service 声明示例。证据见 [验证记录](07-verification-record.md#src-24)。

## 操作顺序

### 1. 先看自己将操作的环境

```bash
kubectl config current-context
kubectl get nodes
```

**[Illustrative safety check]** 先确认当前 context 和 Node 列表，避免把练习 YAML 提交到错误集群。若无权限或没有集群，不要继续；回到前提准备练习环境。

### 2. 提交声明

```bash
kubectl apply -f demo.yaml
```

**[Source-derived · SRC-23]** 编写 YAML，定义 Deployment 与 Service 的期望状态，然后以 `kubectl apply -f demo.yaml` 提交。证据见 [验证记录](07-verification-record.md#src-23)。

**[Research-derived · RSC-05]** `apply -f` 通过资源定义文件创建或更新对象；它是官方推荐的声明式应用管理方式。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 3. 观察是否收敛

```bash
kubectl get deployments
kubectl get pods
kubectl get services
kubectl rollout status deployment/demo-deploy
```

预期观察：Deployment 的 READY/AVAILABLE 逐步达到目标副本数；Pod 会进入可运行状态；Service 存在。具体 Pod 名称、IP、拉取速度和完成时间取决于你的集群，不能从本文推断。

**[Source-derived · SRC-25]** 可用 `get` 查看 Pod、Deployment、Service。证据见 [验证记录](07-verification-record.md#src-25)。**[Research-derived · RSC-05]** `rollout status` 用于等待或观察 Deployment 滚动发布状态。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 4. 更新镜像并观察 rollout

将 YAML 中 `nginx:1.25` 改为 `nginx:1.26`，再执行：

```bash
kubectl apply -f demo.yaml
kubectl rollout status deployment/demo-deploy
```

**[Source-derived · SRC-26]** 修改 YAML 后再次 apply 会更新资源；源材料以镜像版本更新说明这一流程。证据见 [验证记录](07-verification-record.md#src-26)。**[Research-derived · RSC-03]** 镜像位于 Pod 模板，修改它会触发 Deployment rollout。证据见 [验证记录](07-verification-record.md#rsc-03)。

### 5. 排查一个未就绪 Pod

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

先用 `get` 确认对象及状态，再用 `describe` 查看事件，最后读取容器日志。多容器 Pod 需要加 `-c <container-name>`；刚重启后的容器可使用 `--previous` 读取前一实例日志。

**[Source-derived · SRC-25]** `describe` 用于查看 Pod 详细事件，`logs` 用于查看容器日志。证据见 [验证记录](07-verification-record.md#src-25)。**[Research-derived · RSC-05]** `logs` 支持 `-c`、`--previous` 等定位选项。证据见 [验证记录](07-verification-record.md#rsc-05)。

### 6. 清理练习资源

```bash
kubectl delete -f demo.yaml
```

**[Source-derived · SRC-26]** `kubectl delete -f demo.yaml` 删除由该文件定义的资源。证据见 [验证记录](07-verification-record.md#src-26)。执行后再运行 `kubectl get deployments,pods,services`，确认练习资源不再存在。

## 完成检查

完成本章时，应能回答：

- `apply` 后为什么不能只盯住某个 Pod 名称？
- `get`、`describe`、`logs` 分别回答什么问题？
- 为什么更新镜像后要看 `rollout status`？
- 清理时为何用与创建相同的 YAML 文件？

下一节：[相关工具](05-related-tools.md)。
