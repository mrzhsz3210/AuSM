# AuSM Profiles

Profile 定义 AuSM 在特定宿主、存储或运行环境中的实现方式。Core 规定核心语义；Profile 规定文件、数据库、API、调度、容量、兼容与访问控制等具体选择。

## 参考实现

| Profile | 宿主 | 状态 | 实现 |
| --- | --- | --- | --- |
| Memory Diary Profile v0.1 | AstrBot | 首个参考实现；已有真实插件 | [记忆日记](https://github.com/mrzhsz3210/astrbot_plugin_memory_diary) |

Memory Diary Profile 的完整物理 Schema 与生命周期保留在记忆日记仓库的 `docs/spec/MemoryDiaryProfile-v0.1.md`，随该仓库的文档更新发布。本索引只链接实现主页，避免在发布前制造失效的深链接。

## 实现新 Profile 时

1. 阅读 [AuSM Core Spec v0.1](../docs/spec/AuSM-Core-v0.1.md)，确定实现属于 Exchange-only、Assisted 或 Full。
2. 使用 [Core JSON Schema](../schemas/AuSM-Core-v0.1.schema.json) 验证交换文档。
3. 明确文件或表结构、时间与时区、作用域、证据引用、快照失败行为、交接预算和导出排除项。
4. 按 [Conformance](../conformance/README.md) 建立测试，并公开未通过或不适用项。
5. 不复制其他 Profile 的宿主参数来假装兼容；Core 不变量相同，物理实现可以不同。

## 当前空白

- 第二宿主 Profile 尚未形成。
- 第三方独立实现尚未形成。
- 跨宿主 Core-Interchange 尚未完成。

欢迎通过 Issue 提出新 Profile，或通过 Pull Request 添加实现索引与一致性证据。
