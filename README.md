# AuSM

**Language**: English | [简体中文](README.zh-CN.md)

**Autobiographical Snapshot Memory**

> Preserve shared experiences as autobiographical history, express current understanding through rewritable snapshots, and maintain cross-context continuity through evidence tracing and bounded handoff.

AuSM is a context-continuity approach for LLM-based conversational agents proposed by [@mrzhsz3210](https://github.com/mrzhsz3210). This repository maintains the platform-neutral Core specification, a machine-readable exchange model, conformance contracts, and an index of host implementations.

**Current version**: v0.1 Public Technical Draft  
**First public draft date**: 2026-11-08  
**Original proposer and draft author**: [@mrzhsz3210](https://github.com/mrzhsz3210)

The AuSM name, core structure, and initial specification are recorded here. The project does not claim that snapshots, summarization, first-person narratives, retrieval, reflection, or context injection were invented by this project, and it does not present a design draft as proven universal superiority.

## Read the Specification

- **[AuSM Core Spec v0.1 — English](docs/spec/AuSM-Core-v0.1.en.md)**
- **[AuSM Core Spec v0.1 — 简体中文](docs/spec/AuSM-Core-v0.1.md)**
- **[AuSM Core JSON Schema v0.1](schemas/AuSM-Core-v0.1.schema.json)**: the machine-readable `AuSMExchange/0.1` model.
- **[Profiles](profiles/README.md)**: reference implementations and host Profiles.
- **[Conformance](conformance/README.md)**: claim levels, minimum test coverage, and current progress.

The Chinese document is the initial source draft. The English version is intended to be semantically equivalent; subsequent revisions should update both.

## Core Structure

| Component | Question answered |
| --- | --- |
| **Autobiographical history** | What happened, and how did the agent narrate it at the time? |
| **Current snapshot** | After these experiences, how does the agent understand the present? |
| **Evidence tracing** | Which history, quotation, or external source supports that understanding? |
| **Bounded handoff** | What should the next context read, how much, and under which scope? |

AuSM does not exclude RAG, FTS, vector retrieval, or reranking. Core defines their position in the architecture: **retrieval provides on-demand evidence, while snapshots provide current understanding; neither may impersonate the other.**

## What Platform Independence Means

Core does not require AstrBot, QQ, Python, ZIP, a particular database, or a particular model vendor. Hosts may implement Exchange-only, Assisted, or Full AuSM using their own storage, retrieval, and scheduling choices.

Platform independence does not follow automatically from abstract wording. At least two different hosts must read and write the same golden corpus, preserve core semantics, and pass Core-Structural and Core-Lifecycle validation. The accurate current status is:

- Core and its independent Schema are available as v0.1 drafts;
- Memory Diary provides the first real reference implementation;
- A second host, a cross-host golden corpus, and automated interoperability validation remain to be completed;
- Public research evidence from matched-budget baselines, ablations, scale tests, and longitudinal blind evaluation has not yet been produced.

## First Reference Implementation

[Memory Diary (`astrbot_plugin_memory_diary`)](https://github.com/mrzhsz3210/astrbot_plugin_memory_diary) is the first AstrBot implementation of AuSM. It uses ZIP＋Markdown, signed sidecars, a `current_self` snapshot, progressive injection, and local evidence retrieval. These physical choices belong to Memory Diary Profile and are not requirements for every AuSM implementation.

## Repository Structure

```text
AuSM/
├── README.md                 # English home
├── README.zh-CN.md           # 中文首页
├── CITATION.cff
├── LICENSE                   # MIT for Schema and code
├── LICENSE-docs              # CC BY 4.0 notice for prose
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

## Citation

This repository provides [CITATION.cff](CITATION.cff). Use a permalink to a specific commit when citing the draft so that the version remains fixed.

```text
mrzhsz3210. Autobiographical Snapshot Memory (AuSM) Core Spec v0.1:
A platform-independent draft for autobiographical history, mutable snapshots,
and evidence-linked context handoff. 2026-11-08.
```

## License

Copyright © 2026 mrzhsz3210.

- **Prose specifications and documentation**: [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE-docs). You may share and adapt them, including commercially, provided that you give appropriate attribution and indicate changes.
- **JSON Schema and current or future validation code**: [MIT License](LICENSE).

The licenses apply by component rather than offering an either/or choice. Third-party implementations and quotations of prior work remain subject to their own terms.

## Discussion and Implementation

Issues and pull requests are welcome for Core semantics, alternative Profiles, interoperability tests, related work, and negative results. Comparative claims should include reproducible evidence; recording the origin of the AuSM name does not replace related-work analysis or experiments.
