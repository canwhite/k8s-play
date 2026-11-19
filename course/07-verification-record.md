# 07. 验证记录

## 学习目标

能追溯课程中的原始结论与官方补充来源，区分已验证的文档结构、尚未执行的集群运行验证，以及课程的明确边界。

## 根源文件

- Canonical source path: `/Users/doing/Desktop/k8s-play/source.md`
- SHA-256: `a0ff3a7ff36c0a66193bd00bfe1c37021bf458e35c7ebdd67e78beb01790c9de`
- Source treatment: read-only; no source bytes altered.
- Classification: public technical primer. 官方 Kubernetes 文档仅用于补足原始材料未覆盖的安装前提、对象细节、命令语义、配置安全与学习环境工具。

## Source locator map

| ID | Source heading / lines | Supported course sections |
| --- | --- | --- |
| <a id="src-01"></a>SRC-01 | Opening paragraph, lines 1, 3-7 | 01 |
| <a id="src-02"></a>SRC-02 | 核心定位, lines 5-7 | 01 |
| <a id="src-03"></a>SRC-03 | 核心概念, lines 9-11, 98 | 01 |
| <a id="src-04"></a>SRC-04 | Opening, line 5; closing, line 98 | 01 |
| <a id="src-05"></a>SRC-05 | 使用方法, lines 37-46, 79-84 | 01 |
| <a id="src-06"></a>SRC-06 | Deployment, lines 16-17 | 01 |
| <a id="src-07"></a>SRC-07 | 核心定位 line 5; core objects lines 11-22 | 02 |
| <a id="src-08"></a>SRC-08 | Pod, lines 13-14 | 02 |
| <a id="src-09"></a>SRC-09 | Deployment, lines 16-17 | 02 |
| <a id="src-10"></a>SRC-10 | Service, line 20 | 02 |
| <a id="src-11"></a>SRC-11 | Namespace and Node, line 22 | 02 |
| <a id="src-12"></a>SRC-12 | YAML labels and selector, lines 54-61, 71-73; Service description, line 20 | 02 |
| <a id="src-13"></a>SRC-13 | 核心功能, line 28 | 03 |
| <a id="src-14"></a>SRC-14 | 核心功能, line 29 | 03 |
| <a id="src-15"></a>SRC-15 | 核心功能, line 30 | 03 |
| <a id="src-16"></a>SRC-16 | 核心功能, line 31 | 03 |
| <a id="src-17"></a>SRC-17 | 核心功能, lines 32-33 | 03 |
| <a id="src-18"></a>SRC-18 | 核心功能, lines 34-35 | 03 |
| <a id="src-19"></a>SRC-19 | 使用方法, lines 39-41, 45-46, 79-80, 86-93 | 03 |
| <a id="src-20"></a>SRC-20 | Pod, line 14; Service, line 20 | 03 |
| <a id="src-21"></a>SRC-21 | 更新与删除, lines 95-96 | 03 |
| <a id="src-22"></a>SRC-22 | 使用方法, lines 37-41 | 04 |
| <a id="src-23"></a>SRC-23 | 使用方法, lines 43-46 | 04 |
| <a id="src-24"></a>SRC-24 | 应用声明文件, lines 48-77 | 04 |
| <a id="src-25"></a>SRC-25 | 日常观察与排查, lines 86-93 | 04 |
| <a id="src-26"></a>SRC-26 | 更新与删除, lines 95-96 | 04 |
| <a id="src-27"></a>SRC-27 | 应用声明文件, lines 79-84 | legacy: replaced by SRC-24 in expanded 04 |
| <a id="src-28"></a>SRC-28 | 日常观察与排查, lines 86-93 | legacy: replaced by SRC-25 in expanded 04 |
| <a id="src-29"></a>SRC-29 | 使用方法, lines 37-41 | 05 |
| <a id="src-30"></a>SRC-30 | 使用方法, lines 79-93 | 05 |
| <a id="src-31"></a>SRC-31 | 使用方法, lines 37-41 | 05 |
| <a id="src-32"></a>SRC-32 | 核心定位, lines 5-7 | 06 |
| <a id="src-33"></a>SRC-33 | 核心概念, lines 11-20 | 06 |
| <a id="src-34"></a>SRC-34 | 编写声明文件, lines 45-46, 48-77 | 06 |
| <a id="src-35"></a>SRC-35 | 使用方法, lines 79-93 | 06 |
| <a id="src-36"></a>SRC-36 | 更新与删除, lines 95-96 | 06 |
| <a id="src-37"></a>SRC-37 | 日常观察与排查, lines 86-91 | 06 |
| <a id="src-38"></a>SRC-38 | Service, line 20 | 06 |
| <a id="src-39"></a>SRC-39 | YAML example, lines 54-61, 71-73 | 06 |

## Research-derived sources

All pages retrieved 2026-11-19. Confidence is high: each is official Kubernetes documentation and was read before use.

| ID | Official URL | Supporting section / paraphrase | Affected sections | Confidence |
| --- | --- | --- | --- | --- |
| <a id="rsc-01"></a>RSC-01 | [Kubernetes overview](https://kubernetes.io/docs/concepts/overview/) | Overview; Why you need Kubernetes: portable, extensible platform for containerized workloads; declarative desired state; independent controls drive actual state toward it; nodes supply capacity. | 01, 02 | High |
| <a id="rsc-02"></a>RSC-02 | [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) | What is a Pod; Resource sharing and communication; Pods and controllers: smallest deployable units; shared network/storage; disposable lifecycle; controllers manage replication and replacement. | 02, 06 | High |
| <a id="rsc-03"></a>RSC-03 | [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) | Deployments; Scaling; Updating; Rolling Back: replicas target, ReplicaSet management, Pod-template-triggered rollout, rollout status, rollback scope. | 02, 03, 04, 06 | High |
| <a id="rsc-04"></a>RSC-04 | [Service](https://kubernetes.io/docs/concepts/services-networking/service/) | Services in Kubernetes; Defining a Service; Discovering services: stable logical endpoint group, selector-backed Pod discovery, EndpointSlices, Service DNS. | 02 | High |
| <a id="rsc-05"></a>RSC-05 | [kubectl quick reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/) | apply, viewing/finding resources, interacting with Pods, updating, deleting: file-based apply; get/describe/logs options; rollout status; delete. | 01, 03, 04, 05, 06 | High |
| <a id="rsc-06"></a>RSC-06 | [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/) | Purpose and use: non-confidential key-value config, decoupled from images; env/args/volumes; env consumption is not automatically refreshed. | 03 | High |
| <a id="rsc-07"></a>RSC-07 | [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) | Purpose; caution; information security: Secrets contain sensitive data but default storage can be unencrypted; limit access, encrypt at rest, restrict exposure. | 03 | High |
| <a id="rsc-08"></a>RSC-08 | [Install tools](https://kubernetes.io/docs/tasks/tools/) | kubectl command-line tool; learning environment; kind requires Docker/Podman; minikube provides local clusters for supported desktop OSs. | 04, 05, 06 | High |

## Evidence gaps and explicit boundaries

| ID | Claim needed | Affected section | Reason / course treatment |
| --- | --- | --- | --- |
| GAP-01 | A cluster-specific command result | 04, 06 | No authorized Kubernetes cluster is supplied. Commands show official semantics and expected observations, never this repository's actual output. |
| GAP-02 | Helm, Kustomize, Dashboard, GitOps comparisons | 05 | Outside source and researched evidence. Mentioned only as excluded topics; no comparative claims made. |
| GAP-03 | Production storage, networking, RBAC, multi-cluster operating guidance | 03–06 | Outside beginner scope. The course names relevant boundaries but does not prescribe designs. |

## Research record

- External research classification: permitted public source; no private project text, credentials, identifiers, or cluster data sent externally.
- Research scope: eight Kubernetes-owned documentation pages needed to fill precise evidence gaps.
- Conflict handling: no conflict found between original primer claims and official documentation. Original material remains primary for every `SRC-*` conclusion; `RSC-*` adds detail only.

## Local link and structure verification

- Required sequence files found: `01` → `02` → `03` → `04` → `05` → `06` → `07`.
- Supplemental closure file: `08-course-post-mortem.md`, linked from chapter 06 and back to chapters 06–07.
- Verification target: all local Markdown links, anchors, evidence IDs, required headings, course order and source checksum.
- Runtime boundary: no Kubernetes cluster commands executed; runtime verification remains a learner action in an authorized environment.

## Review and audit record

- Audit boundary: `course/` and `docs/course-expansion-plan.md`; `source.md` is read-only.
- Pre-mortem: plan updated before writing with provenance, dependency-chain, non-fabricated-runtime, and source-traceability mitigations.
- Course post-mortem: [08-course-post-mortem.md](08-course-post-mortem.md) records scope, chapter handoffs, decisions, verification, limits, root causes and prevention.
- Review focus: terminology consistency, source attribution, command preconditions, expected-vs-observed wording, navigation, and practice lifecycle.
- Expected corrections: retain original `SRC-*`; introduce independent `RSC-*`; mark no-cluster output as expected; limit tool comparisons to official evidence.
- Unresolved HITL conflicts: none. A real cluster run remains intentionally out of scope until an authorized context is supplied.
