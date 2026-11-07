# AuSM

**语言**：[English](README.md) | 简体中文

**自传快照记忆（Autobiographical Snapshot Memory）**

> 以自传历史保存共同经历，以可重写快照表达当前理解，以证据回源和受限交接维持跨上下文连续性。

AuSM 是由 [@mrzhsz3210](https://github.com/mrzhsz3210) 提出的、面向 LLM 对话 Agent 的上下文连续性技术路线。本仓库维护平台无关的核心规范、机器可读交换模型、一致性契约与不同宿主的实现索引。

**当前版本**：v0.1 Public Technical Draft  
**首次公开发布日期**：2026-11-08  
**原始提出者与草案作者**：[@mrzhsz3210](https://github.com/mrzhsz3210)

AuSM 的名称、核心结构与初始规范在此留档。本项目不声称快照、摘要、第一人称叙述、检索、反思或上下文注入等单项技术由本项目首创，也不把设计草案冒充为已经验证的普遍优势。

## 阅读草案

- **[AuSM Core Spec v0.1 — English](docs/spec/AuSM-Core-v0.1.en.md)**
- **[AuSM Core Spec v0.1 — 简体中文](docs/spec/AuSM-Core-v0.1.md)**
- **[AuSM Core JSON Schema v0.1](schemas/AuSM-Core-v0.1.schema.json)**：`AuSMExchange/0.1` 的机器可读交换模型。
- **[Profiles](profiles/README.md)**：参考实现与宿主 Profile 索引。
- **[Conformance](conformance/README.md)**：一致性等级、最低测试范围与当前进度。

中文文档是初始来源草案，英文版与其保持语义等价；后续修订应当同步更新两个版本。

## 核心结构

| 组成 | 回答的问题 |
| --- | --- |
| **自传历史** | 当时发生了什么，Agent 当时怎样叙述？ |
| **当前快照** | 经历这些以后，现在的 Agent 怎样理解？ |
| **证据回源** | 这种理解来自哪段历史、原话或外部来源？ |
| **受限交接** | 下一次上下文应该读多少、读哪些、以什么作用域读取？ |

AuSM 不排斥 RAG、FTS、向量检索或重排。Core 规定的是它们在路线中的位置：**检索负责按需回源，快照负责当前理解，二者不得互相冒充。**

## 平台无关是什么意思

Core 不要求 AstrBot、QQ、Python、ZIP、特定数据库或特定模型厂商。宿主可以按自身能力实现 Exchange-only、Assisted 或 Full AuSM，并选择自己的存储、检索和调度方式。

“平台无关”不是靠抽象措辞自动成立：至少需要两个不同宿主读写同一黄金语料，保持核心语义，并通过 Core-Structural 与 Core-Lifecycle 验证。当前准确状态是：

- Core 规范与独立 Schema 已形成 v0.1 草案；
- 记忆日记提供首个真实参考实现；
- 第二宿主、跨宿主黄金语料与自动化互操作验证尚待完成；
- 同预算基线、消融、规模与长期盲评尚未形成公开论文证据。

## 首个参考实现

[记忆日记（astrbot_plugin_memory_diary）](https://github.com/mrzhsz3210/astrbot_plugin_memory_diary) 是 AuSM 的首个 AstrBot 参考实现。它使用 ZIP＋Markdown、签名侧车、`current_self` 快照、渐进式注入与本地证据检索；这些物理选择属于 Memory Diary Profile，不是所有 AuSM 实现的必要条件。

## 仓库结构

```text
AuSM/
├── README.md                 # English home
├── README.zh-CN.md           # 中文首页
├── CITATION.cff
├── LICENSE                   # Schema 与代码采用 MIT
├── LICENSE-docs              # 正文采用 CC BY 4.0
├── docs/spec/
│   ├── AuSM-Core-v0.1.md     # 中文原始草案
│   └── AuSM-Core-v0.1.en.md  # English version
├── schemas/
│   └── AuSM-Core-v0.1.schema.json
├── conformance/
│   └── README.md
└── profiles/
    └── README.md
```

## 引用

仓库提供 [CITATION.cff](CITATION.cff)。建议使用具体提交的永久链接引用草案，以固定版本。

```text
mrzhsz3210：《自传快照记忆（AuSM）Core Spec v0.1》，公开技术草案，2026-11-08。
```

## 许可

版权所有 © 2026 mrzhsz3210。

- **规范正文与文档**：采用 [知识共享署名 4.0 国际许可（CC BY 4.0）](LICENSE-docs)。可以分享、修改和商用，但必须保留适当署名并标明所作修改。
- **JSON Schema 与现有或后续校验代码**：采用 [MIT 许可证](LICENSE)。

许可按部件分别适用，不是让使用者在两种许可中任选其一。第三方实现及既有工作的引用仍遵循其自身条款。

## 讨论与实现

欢迎通过 Issue 或 Pull Request 讨论核心语义、替代 Profile、互操作测试、相关工作与负面结果。对路线效果的比较应提供可复现证据；命名留档不替代论文的相关工作与实验章节。
