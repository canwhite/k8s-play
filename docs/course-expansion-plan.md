# Plan: Kubernetes 入门课程扩写

> **User's request (restated)**: 开始补充 `course/` 各章节内容；补充完成后进行整体 post-mortem，使课程内容清晰、结构完整、逻辑闭环。
>
> **What this plan answers**: 将现有七章提纲扩成可追踪、可实践、可验收的 Kubernetes 入门课程。
>
> Goal: 保留原始 `source.md`，用 Kubernetes 官方资料补齐准确上下文、操作步骤、练习与工具边界，并完成全课程审查。

## Context

现有课程严格来自单一简介材料，准确但章节内容不足。`course/` 已包含七个目标文件，因此本次在既有文件内扩写，不新增章节。原材料为公开技术介绍，允许按缺口检索官方 Kubernetes 文档。所有扩展事实须具有逐项可追溯来源。

## Goal

七章均有明确学习目标、分层解释、至少一个可验证示例或练习、内部导航和可追溯证据；课程由概念、对象关系、能力边界、操作、工具选择、学习路线连成闭环。

## Plan

1. 盘点 `source.md` 与现有课程，建立原始主张、扩展事实、章节目标映射。
2. 仅检索 Kubernetes 官方文档，补足安装/集群前提、对象行为、观测命令、工作负载、配置、工具边界等证据。
3. 在原有七个 Markdown 文件中扩写：每章加入解释、场景、练习或检查；更新验证记录，登记 URL、检索日期、支持内容与置信度。
4. 执行全课程 post-mortem：检查章节依赖、术语一致性、断言证据、练习可行性、链接及逻辑闭环；立即修复发现问题。
5. 验证 Markdown 结构、内部链接、来源映射、源文件校验值与章节顺序；记录结果。

## Think — Debug Methodology

- 内容边界：先读取原始材料与课程源文件；外部资料只用于确实缺失的事实。
- 证据边界：每个研究衍生断言在 `07-verification-record.md` 记录具体官方 URL、支持段落和受影响章节；不以搜索摘要作为证据。
- 结构边界：从学习路径反向追踪前置概念，确认每项实操在前文已定义。
- 定位方式：先检查验证记录的来源 ID，再检查引用它的章节，最后检查练习的命令与预期结果是否匹配。

## Do — Verification Strategy

- **Markdown structure**: 用脚本确认七个规定文件、一级标题、学习目标、顺序导航均存在。
- **Local links**: 解析所有 Markdown 链接，确认目标存在且锚点有效。
- **Evidence**: 检查每个 `Source-derived`/`Research-derived` 标记都有可解析记录 ID；每个研究 ID 都有 Kubernetes 官方 URL、检索日期和支持内容。
- **Logic correctness**: 人工走读“场景 → YAML → apply → observe → update → diagnose → clean up”链路；确认所有命令的前提在课程中声明。
- **Runtime**: N/A — 仓库未提供可用集群或应用运行环境；课程将明确把集群访问列为前提，不伪造执行输出。

## Adjust — Rollback and Global Scan

- **Rollback plan**: 通过 Git 恢复七个课程文件和本计划文件；`source.md` 全程只读。
- **Global scan**: 搜索所有章节的证据标签、术语、命令、导航链接，避免单章扩展而相邻章节仍存在同类缺口。
- **Backwards compatibility**: 文件名、章节顺序、原始来源 ID 均保留；新增研究来源使用独立 `RSC-*` ID。

## Open Questions

- N/A — 课程定位为 K8s 初学者，默认使用本地练习集群或已有可访问集群；具体安装工具以官方安装页为准。

## Out of Scope

- 不创建真实 Kubernetes 集群、不执行会修改用户集群的 `kubectl` 命令。
- 不覆盖生产级网络、存储、安全策略或多集群运维教程。
- 不修改 `source.md`。

---

> Next step: execute the scoped course expansion and verification gates.

---

## Pre-Mortem Risks

### [Risk] 扩写引入未证实或过期断言

**Severity**: 5 | **Likelihood**: 3 | **Detectability**: 0.1
**Risk Score**: 13.5

**Failure Scenario**: 课程从提纲变长，但安装、行为或工具比较依赖非官方或过期资料，学习者据此执行后得到错误结果。

**Mitigation**:
- 每个研究衍生断言仅引用 Kubernetes 官方文档，并在验证记录中登记 URL、检索日期、支持内容与章节。
- 链接与来源 ID 校验失败时，不保留对应断言；缺证据处明确为边界或待补信息。

### [Risk] 章节变长但学习链断裂

**Severity**: 4 | **Likelihood**: 4 | **Detectability**: 0.2
**Risk Score**: 12.8

**Failure Scenario**: 课程包含更多概念和命令，却在定义前使用术语，或练习无法由前文知识完成。

**Mitigation**:
- 按“概念 → 对象关系 → 能力与边界 → 操作 → 工具选择 → 学习路径”固定依赖顺序写作。
- 审查阶段从学习路径反向走读每项练习，逐项检查前提、命令、可观察结果和清理动作。

### [Risk] 课程伪造已执行的集群结果

**Severity**: 4 | **Likelihood**: 3 | **Detectability**: 0.1
**Risk Score**: 10.8

**Failure Scenario**: 没有可用集群仍把期望描述写成实际输出，误导学习者认为环境已验证。

**Mitigation**:
- 将命令结果标成“预期观察”而非实测输出，并把可访问集群列为实践前提。
- 最终记录明确区分文档/链接验证与未执行的集群运行验证。

### [Risk] 现有课程文件被整体替换而丢失原始可追溯性

**Severity**: 4 | **Likelihood**: 2 | **Detectability**: 0.2
**Risk Score**: 6.4

**Failure Scenario**: 扩写删除原有 `SRC-*` 标记，导致原始材料与课程结论失去对应关系。

**Mitigation**:
- 保留原有 `SRC-*` 证据编号；新资料一律用独立 `RSC-*` 编号。
- 验证时检查每个源材料主张仍能解析到验证记录。
