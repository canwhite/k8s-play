# 07. 验证记录

## 根源文件

- Canonical source path: `/Users/doing/Desktop/k8s-play/source.md`
- SHA-256: `a0ff3a7ff36c0a66193bd00bfe1c37021bf458e35c7ebdd67e78beb01790c9de`
- Source treatment: read-only; no source bytes altered.
- Classification: public-style technical primer. No external research performed because source supports all included factual claims; installation and comparison gaps stay explicit.

## Source locator map

| ID | Source heading / lines | Supported course sections |
| --- | --- | --- |
| <a id="src-01"></a>SRC-01 | Opening paragraph, lines 1, 3-7 | 01 |
| <a id="src-02"></a>SRC-02 | 核心定位, lines 5-7 | 01 |
| <a id="src-03"></a>SRC-03 | 核心概念, lines 9-11, 98 | 01 |
| <a id="src-04"></a>SRC-04 | Opening, lines 5; closing, line 98 | 01 |
| <a id="src-05"></a>SRC-05 | 使用方法, lines 37-46, 79-84 | 01 |
| <a id="src-06"></a>SRC-06 | Deployment, lines 16-17 | 01 |
| <a id="src-07"></a>SRC-07 | 核心定位 lines 5; core objects lines 11-22 | 02 |
| <a id="src-08"></a>SRC-08 | Pod, lines 13-14 | 02 |
| <a id="src-09"></a>SRC-09 | Deployment, lines 16-17 | 02 |
| <a id="src-10"></a>SRC-10 | Service, lines 19-20 | 02 |
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
| <a id="src-24"></a>SRC-24 | 应用声明文件, lines 79-84 | 04 |
| <a id="src-25"></a>SRC-25 | 日常观察与排查, lines 86-93 | 04 |
| <a id="src-26"></a>SRC-26 | 更新与删除, lines 95-96 | 04 |
| <a id="src-27"></a>SRC-27 | 应用声明文件, lines 79-84 | 04 |
| <a id="src-28"></a>SRC-28 | 日常观察与排查, lines 86-93 | 04 |
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

## Evidence gaps

| ID | Claim needed | Affected section | Why source is insufficient | Status |
| --- | --- | --- | --- | --- |
| GAP-01 | `kubectl` installation, cluster access, configuration | 04 | Source gives commands but no environment setup instructions. | Unresolved; no research requested or needed for source-only course. |
| <a id="gap-02"></a>GAP-02 | Comparison with other K8s-related tools | 05 | Source names only `kubectl`; no comparison evidence. | Unresolved; section marked Not applicable. |

## Research record

No external research performed. No research-derived claims included.

## Local link check

Expected internal links: 01→02, 02→03, 03→04, 04→05, 05→06, 06→07. Each target is a required staged file.

## Staging manifest

- Canonical staging path: `/Users/doing/Desktop/k8s-play/.course-staging.VpOETn`
- Content manifest: `content.sha256` (only seven required Markdown files)
- Metadata manifest: `manifest.md` (source, boundary, and prohibited-path record)
- Manifest checksum is external promotion evidence: it is generated and verified after this record is finalized, so this file does not embed a self-invalidating checksum.

## Review and audit record

- Audit mode: review-only; bounded to staged files named by manifest.
- Per-section review invoked for all seven required files. No content edits were made by the review pass.
- Cross-section consistency: terminology, required order, source IDs, and sequence links manually reconciled.
- Local-link verification: 7 ordered Markdown files found; no dangling local Markdown links.
- Content-manifest verification: all seven required Markdown files matched their staged checksums. The combined metadata/checksum file emitted four formatting warnings under `shasum -c`; this was an audit-tooling defect, not a content checksum failure. Split manifests correct it.
- Edits after audit: this verification record now documents the split-manifest correction; final checksums must be regenerated before promotion approval.
- Unresolved HITL conflicts: none.
- Promotion state: staged only; no destination files created.
