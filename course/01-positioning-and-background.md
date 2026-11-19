# 01. 定位与背景

## 学习目标

完成本章后，你能说明 Kubernetes（K8s）解决什么问题、何时适合使用，并用“期望状态 → 控制器 → 实际状态”解释其工作方式。

## K8s 是什么

**[Source-derived · SRC-01]** Kubernetes 是容器编排平台，面向规模化场景中自动、可靠地管理大量容器的需求。它将多台机器抽象为统一资源池，再借助声明式配置与自动化控制维持期望状态。证据见 [验证记录](07-verification-record.md#src-01)。

**[Research-derived · RSC-01]** Kubernetes 是管理容器化工作负载和服务的可移植、可扩展开源平台；它使用声明式配置与自动化。你提交目标状态，独立控制进程持续让实际状态向目标收敛，而不是按一次性脚本顺序完成任务。证据见 [验证记录](07-verification-record.md#rsc-01)。

## 它解决的问题

单机上运行少量容器时，手动启动、重启、改端口仍可能可控。副本增多、机器变多、版本要逐步更新时，手工维护“哪些容器应存在、流量该到哪里、失败后谁补齐”会变成持续工作。

**[Research-derived · RSC-01]** K8s 为分布式系统提供扩缩、故障转移和部署模式。它不承诺应用永不失败；它的职责是根据已声明规则持续处理偏离目标的状态。证据见 [验证记录](07-verification-record.md#rsc-01)。

## 心智模型：声明目标，不写操作剧本

把 YAML 看成期望清单：`“运行 3 个这个版本的应用，并给它一个稳定入口。”` K8s 控制器持续比较清单与集群现状：少副本时创建或替换 Pod；新模板出现时逐步建立新版本；后端 Pod 变化时更新访问端点。

```text
YAML 期望状态
      ↓ apply
API 中保存的目标
      ↓ 控制器持续比较
实际 Pod / 网络端点 / 副本数
      ↑ 偏离时被调节
```

**[Illustrative]** 若目标是 3 个 Web Pod，而某个 Pod 消失，课程后续会学习的 Deployment 控制器会创建替代 Pod；Service 则继续选择符合标签的后端。此图说明控制环思路，不代表对任何特定集群的实测输出。

## 面向谁、何时开始

**[Source-derived · SRC-03]** 本课程适合刚接触 K8s、需要先理解 Pod、Deployment、Service 协作关系的学习者。源材料明确建议入门先聚焦这三个对象。证据见 [验证记录](07-verification-record.md#src-03)。

适合开始学习的情形：你已有容器镜像，想让应用以多个副本运行、稳定被其他服务访问、或以声明文件重复部署。若目标只是本机启动一个临时容器，K8s 会引入额外对象和运维成本；先掌握容器基础通常更直接。

## 完成后能做到什么

- **[Source-derived · SRC-04]** 解释声明式 API 与控制器模式：用户描述目标状态，控制循环持续让实际状态收敛。证据见 [验证记录](07-verification-record.md#src-04)。
- **[Source-derived · SRC-05]** 读懂一个包含 Deployment 和 Service 的基本 YAML，并用 `kubectl apply -f` 提交。证据见 [验证记录](07-verification-record.md#src-05)。
- **[Research-derived · RSC-05]** 用 `get`、`describe`、`logs` 和 `rollout status` 区分资源列表、事件、容器输出、发布状态。证据见 [验证记录](07-verification-record.md#rsc-05)。

## 自检

用自己的话回答：YAML 中写 `replicas: 3` 是在命令 K8s “立刻运行某三个固定容器”，还是在声明一个持续要满足的状态？答案应是后者；Pod 身份可变化，目标副本数不变。

下一节：[核心概念](02-core-concepts.md)。
