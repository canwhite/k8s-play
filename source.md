Kubernetes（简称 K8s）的核心定位是**容器编排平台**。它解决的，是在规模化场景下“如何自动、可靠地管理成千上万个容器”的问题。

### 🎯 核心定位：集群的“自动驾驶系统”

K8s 并不是简单地运行容器，而是将多台机器抽象成一个**统一的资源池**，并通过**声明式配置**和**自动化控制**，让应用始终维持在用户期望的状态。

它基于 Google 十余年的大规模生产经验设计，核心价值在于**将运维人员从繁琐的手动操作中解放出来**，专注于应用本身，而不是底层基础设施的琐碎管理。同时，它保持开放和可扩展，不限制应用类型，也不强制绑定特定的技术栈。

### 🧱 核心概念：三个对象构成的理解地图

入门 K8s，抓住 **Pod、Deployment、Service** 这三个核心对象，就足以理解其基本运作逻辑。

**1. Pod：最小的调度单元**
Pod 是 K8s 中可部署的最小单元，可以看作容器的“外壳”。一个 Pod 可以包含一个或多个紧密协作的容器，它们**共享网络和存储**，总是一起被调度到同一节点上，同生共死。Pod 本身是**短暂的**，节点故障或升级都会导致其被替换，因此不应用于持久化数据。

**2. Deployment：无状态应用的“管家”**
由于 Pod 自身不具备自愈和扩缩能力，生产环境通常使用 Deployment 来管理它们。你只需声明期望状态（例如“运行 3 个 nginx 副本”），Deployment 就会通过控制器**确保实际状态与之相符**：副本少了自动补齐，Pod 故障了自动重建，并能实现**滚动更新与一键回滚**，保证服务不中断。

**3. Service：稳定的访问入口**
Pod 的 IP 地址是临时的，重启后会改变。Service 的作用就是为一组 Pod 提供一个**固定不变的虚拟 IP（ClusterIP）和 DNS 名称**。它通过**标签选择器**动态关联后端 Pod，并将请求**负载均衡**到这些 Pod 上，从而彻底解耦了“服务访问”与“Pod 生命周期”。Service 有多种类型，如仅集群内可访问的 `ClusterIP`、通过节点端口暴露的 `NodePort`，以及在云上自动创建负载均衡器的 `LoadBalancer`。

此外，**Namespace**（命名空间）用于在逻辑上隔离集群内的资源（如按团队或环境划分），**Node**（节点）则是承载 Pod 运行的实际工作机器。

### ⚙️ 核心功能：自动化能力的集中体现

K8s 的核心功能均围绕上述概念展开，旨在实现自动化运维。

*   **服务发现与负载均衡**：无需修改应用，K8s 为 Pod 提供 IP 和 DNS，并自动在 Pod 间分发流量。
*   **自动化上线与回滚**：逐步、受控地更新应用，并在出现问题时自动回滚，确保服务不中断。
*   **自我修复**：自动重启失败的容器、替换无响应的 Pod，并在其就绪前不将流量导入。
*   **水平扩缩**：可根据 CPU 使用率等指标，自动或手动调整 Pod 副本数量。
*   **存储编排**：自动挂载本地、云盘或网络存储（如 NFS），满足有状态应用的持久化需求。
*   **配置与密钥管理**：在不重建镜像的情况下更新应用配置，并安全地管理密码、令牌等敏感信息。
*   **自动装箱（调度）**：根据资源需求，将容器智能地调度到最合适的节点上，以优化资源利用率。
*   **批处理执行**：除了长期运行的服务，也能管理 Job 和 CronJob 等批处理任务。

### 🛠️ 使用方法：通过 `kubectl` 与声明式文件管理集群

使用 K8s 的主流方式是 **`kubectl` 命令行工具** 配合 **YAML 声明文件**。

**基础操作语法**为：`kubectl [command] [TYPE] [NAME] [flags]`。

一个典型的工作流如下：

**1. 编写声明文件**
创建一个 YAML 文件（例如 `demo.yaml`），在其中定义 Deployment 和 Service 的期望状态。核心是 `spec` 部分，描述“我想要什么”。

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
  template:  # Pod 模板
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
---
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  selector:
    app: demo  # 关联上述 Pod
  ports:
  - port: 80
    targetPort: 80
```

**2. 应用声明文件**
使用 `kubectl apply` 将文件提交给集群。K8s 会自动计算差异，并将集群状态向文件描述的状态收敛。

```bash
kubectl apply -f demo.yaml
```

**3. 日常观察与排查**
应用后，使用以下命令查看资源状态：

*   `kubectl get pods`：查看 Pod 列表及状态。
*   `kubectl get deployments`：查看 Deployment 状态。
*   `kubectl get services`：查看 Service 的访问入口信息。
*   `kubectl describe pod <pod-name>`：查看 Pod 的详细事件，用于排查问题。
*   `kubectl logs <pod-name>`：查看容器日志。

**4. 更新与删除**
更新时，直接修改 YAML 文件（如将镜像版本从 `nginx:1.25` 改为 `nginx:1.26`），再次执行 `kubectl apply -f demo.yaml`，即可触发滚动更新。删除资源则使用 `kubectl delete -f demo.yaml`。

K8s 的强大源于其“**声明式 API**”和“**控制器模式**”：你负责描述“最终想要的风景”（YAML 文件），K8s 的控制循环则持续工作，确保现实与这幅风景图一致。入门时聚焦于 Pod、Deployment、Service 这三个对象的配合，就能清晰地把握住它的运作主线。