---
name: writing-handovers
version: "1.0"
description: 为当前 Claude Code session 生成四段式 handover 交接文档（WHY 目标 / WHAT 本轮产出 / STATE 当前状态 / NEXT 下轮 todo），覆盖实验、串行任务、代码开发三类交接，供下一个 session 直接上手；单向只写。仅在用户【显式调用】时使用：用户运行 /writing-handovers，或明确点名要用本 skill。不要基于推断的意图自动触发——即便用户提到"交接 / handover / 换 session / ctx 快满了 / 总结进度 / 给下一棒留说明"，也不要自动触发；本 skill 也不读取、不更新已有 handover。
---

# Writing Handovers (writing-handovers)

## 概述

一轮工作结束时，把当前 session 的上下文压缩成一份 handover 文档，让**下一个 session（或下一棒任务）读了就能直接上手**，不必重新追问或翻历史。

**单向**：本 skill 只负责「写」交接文档。读接棒由下一个 session 自己完成（自然读取 `docs/handovers/` 或用户直接指给它）——刻意不做「取最新/按主题过滤」等读取侧逻辑，保持行为完全确定。

覆盖三类交接，用同一套四段骨架：
- **experiment**：一轮实验结束，给下一轮实验输入。
- **task-chain**：任务 1 完成，交棒给任务 2。
- **codedev**：开发到一半，换 session 继续。

## 何时启动

**仅用户显式调用**（`/writing-handovers` 或口头点名）。不自动触发，不挂 hook。

## 调用

```
/writing-handovers [type] [下轮聚焦什么]
```

- `type` 可选，取值 `experiment` | `task-chain` | `codedev` | `general`。
- 第二段（自由文本）是「下轮聚焦什么」，写入 frontmatter 的 `focus_next`，并据此裁剪 NEXT 段。

## 流程

1. **定 type**：显式传参优先；不传则从当轮对话推断最贴的 type（跑实验→experiment；多任务串接→task-chain；改代码→codedev；都不像→general）。
2. **读模板**：打开 `references/handover-template.md`，复制「核心骨架」。
3. **提炼四段**：从当前对话归纳 WHY / WHAT / STATE / NEXT 填入（各段写法见模板与下方「四段要点」）。
4. **按 type 织入附加字段**：照模板「type 附加字段」表，把额外内容并入对应段（general 不加）。
5. **引用而非复制 + 脱敏**：大产物（output JSON、commit、spec、心跳文件、PR/issue）只写路径 / URL / commit hash；脱敏 API key、cookie、密码、PII。
6. **写文件**：存到**当前工作项目**的 `docs/handovers/<YYYY-MM-DD>-<slug>.md`。
   - `<YYYY-MM-DD>` 用当天日期（不含时间）。
   - `<slug>` 由工作线主题生成的 kebab-case 短串。
   - **重名处理**：若同名文件已存在，加数字后缀 `-2`、`-3`……**绝不静默覆盖**。
   - 目录不存在则创建 `docs/handovers/`。
7. **返回**：打印生成的文件绝对路径。

## 四段要点

- **WHY**：目标/上下文，给接棒者建立心智模型。实验场景在此补**假设**。
- **WHAT**：本轮行为与产出。大产物引用不复制。实验补**可复现步骤&参数**；代码开发补**改动文件&diff 摘要**。
- **STATE**：什么已验证、什么还坏着。接棒者据此判断能信什么。实验补**结果数据&结论可信度**；串行任务补**产出契约**；代码开发补**测试/构建命令&哪里还红**。
- **NEXT**：下一步 todo（checkbox）+ **接棒须知**（建议调用的 skill / 已知坑 / 不要碰的东西）。串行任务补**下游任务明确入口**；代码开发补**如何验证**。

## 硬规矩

- **引用而非复制**：大产物只写路径/URL/commit，不把内容塞进 handover。
- **脱敏**：API key / cookie / 密码 / PII 一律不写进文档。
- **不覆盖**：同名文件加数字后缀。
- **不读不删**：本 skill 不读取、不修改、不删除已有 handover。
