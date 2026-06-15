# writing-handovers

> **Session 交接文档生成器** — 一轮工作结束时，把当前 Claude Code session 的上下文压缩成一份 handover，让下一个 session / 下一棒任务读了就能直接上手。
>
> *One-way session handover writer for Claude Code: compact the current session into a handover doc so the next session can pick up immediately.*

<p>
  <img alt="version 1.0" src="https://img.shields.io/badge/version-1.0-blue">
  <img alt="type: agent skill" src="https://img.shields.io/badge/type-agent%20skill-6f42c1">
  <img alt="direction: write-only" src="https://img.shields.io/badge/direction-write--only-0969da">
  <img alt="trigger: explicit only" src="https://img.shields.io/badge/trigger-explicit%20only-d29922">
</p>

---

## 这是什么

`writing-handovers` 把「换 session 接着干」这件事标准化。一轮工作收尾时显式调用它，它会把对话上下文压缩成一份四段式 handover 文档，落到当前项目的 `docs/handovers/` 下。下一个 session 读这份文档即可直接上手。

**单向（只写）**：只负责生成交接文档，不负责读取/定位——读接棒由下一个 session 自己完成。这样行为完全确定，没有「取最新/猜前驱」的不确定性。

覆盖三类交接，共用一套四段骨架（WHY / WHAT / STATE / NEXT）：

| type | 场景 |
|---|---|
| `experiment` | 一轮实验结束，给下一轮实验输入 |
| `task-chain` | 任务 1 完成，交棒给任务 2 |
| `codedev` | 开发到一半，换 session 继续 |
| `general` | 不分类，纯四段 |

## 用法

```
/writing-handovers [type] [下轮聚焦什么]
```

- `type` 可选；不传则从当轮对话自动推断。
- 产物路径：`<当前项目>/docs/handovers/<YYYY-MM-DD>-<slug>.md`（同名不覆盖，加 `-2`/`-3` 后缀）。

## 设计原则

- **引用而非复制**：大产物（JSON、commit、spec）只写路径/URL，不塞内容。
- **脱敏**：API key / cookie / PII 不入文档。
- **显式触发**：不自动跑，不挂 hook。

详见 [`DESIGN.md`](./DESIGN.md)。
