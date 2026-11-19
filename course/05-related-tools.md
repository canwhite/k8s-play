# 05. 相关工具

## 学习目标

能选择本课程场景中的 `kubectl` 与本地学习环境工具；能区分“管理已有集群”和“创建练习集群”。

## kubectl：管理集群资源

**[Source-derived · SRC-29]** `kubectl` 是源材料明确列出的命令行工具，用于配合 YAML 声明文件管理集群。证据见 [验证记录](07-verification-record.md#src-29)。

**[Research-derived · RSC-08]** 官方将 `kubectl` 定义为向 Kubernetes 集群执行命令的工具，可部署应用、检查和管理集群资源、查看日志。它要求已安装 CLI、已配置集群访问。证据见 [验证记录](07-verification-record.md#rsc-08)。

**[Source-derived example · SRC-30]** `kubectl apply -f demo.yaml` 提交声明；`kubectl get pods` 查看 Pod 列表和状态。证据见 [验证记录](07-verification-record.md#src-30)。

### 本课程常用命令组

| 目标 | 命令 | 观察点 |
| --- | --- | --- |
| 创建或更新声明 | `kubectl apply -f demo.yaml` | API 接受声明；后续看资源收敛 |
| 列出状态 | `kubectl get deployments,pods,services` | 是否创建、是否可用 |
| 看对象事件 | `kubectl describe pod <pod-name>` | 调度、镜像、探针等事件 |
| 看容器输出 | `kubectl logs <pod-name>` | 应用标准输出；需要时加 `-c` 或 `--previous` |
| 等待发布 | `kubectl rollout status deployment/demo-deploy` | rollout 是否完成 |
| 清理资源 | `kubectl delete -f demo.yaml` | 同一声明文件定义的对象被移除 |

**[Research-derived · RSC-05]** 以上命令的用途与选项来自官方 kubectl quick reference。证据见 [验证记录](07-verification-record.md#rsc-05)。

## 本地学习环境：kind 与 minikube

当你还没有集群时，需要的不是“另一个 kubectl”，而是能提供 Kubernetes API 和 Node 的练习环境。

| 工具 | 角色 | 选择条件 | 与 kubectl 的关系 |
| --- | --- | --- | --- |
| kind | 用 Docker 或 Podman 创建本地 Kubernetes 集群 | 本机已有 Docker 或 Podman，想快速创建容器化练习集群 | 创建好集群后仍用 kubectl 管理资源 |
| minikube | 在 Windows、macOS、Linux 提供本地 Kubernetes 集群 | 想跟随其入门流程做本地开发或试用 | 创建好集群后仍用 kubectl 管理资源 |
| kubectl | 对已有集群发出管理命令 | 已有可访问集群或已由本地工具创建集群 | 不负责自己提供集群 |

**[Research-derived · RSC-08]** 官方工具页面说明：kind 要求 Docker 或 Podman；minikube 面向 Windows、macOS、Linux 的本地集群；两者用于准备环境，`kubectl` 用于与集群交互。证据见 [验证记录](07-verification-record.md#rsc-08)。

## 选择规则

1. 已有公司、云或学习集群 → 安装和配置 `kubectl`，先确认 context。
2. 没有集群，只做本机学习 → 选择 kind 或 minikube 的官方入门流程，随后用 `kubectl` 执行本课程。
3. 需要重复部署同一组资源 → 将 YAML 纳入版本控制，仍用 `kubectl apply -f` 管理声明。

**[Source-derived · SRC-31]** 当目标是通过声明式文件管理 K8s 集群时，源材料给出的工具是 `kubectl`。证据见 [验证记录](07-verification-record.md#src-31)。

## 本章边界

Helm、Kustomize、Dashboard、GitOps 等工具对真实项目也常见，但原始材料未涉及，且本章的官方证据只覆盖 `kubectl`、kind、minikube。因此不将它们的功能或比较写成本课程结论。先掌握 YAML 与 `kubectl`，再按团队工作流选择上层工具。

下一节：[学习路径](06-learning-path.md)。
