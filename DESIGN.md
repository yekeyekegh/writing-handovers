# writing-handovers — 设计要点

> 日期：2026-06-15
> 完整设计 spec：[`docs/superpowers/specs/2026-06-15-writing-handovers-design.md`](./docs/superpowers/specs/2026-06-15-writing-handovers-design.md)

## 一句话定位

用户显式调用的、**单向（只写）**的 session 交接 skill：把当前 session 上下文压缩成四段式 handover，供下一棒直接上手。

## 关键决策

1. **单向**：只写不读。去掉「取最新/按主题过滤定位前驱」等读取侧逻辑，换取行为完全确定。读接棒由下一个 session 自己做。
2. **统一四段骨架 + 可选 type 微调**：WHY / WHAT / STATE / NEXT 覆盖 ~80%；`type`（experiment/task-chain/codedev/general）只在对应段织入附加字段。单核心模板，不为每个 type 复制整套。
3. **存储**：`<当前项目>/docs/handovers/<YYYY-MM-DD>-<slug>.md`；同名加数字后缀，绝不覆盖。是否进 git 交各项目 `.gitignore` 决定。
4. **借鉴 mattpocock/skills@handoff**：引用而非复制、接棒须知段。

## 非目标（YAGNI）

- 不做读取/接棒侧逻辑。
- 不做跨项目全局索引、不做 handover 间链式引用追踪。
- 不自动触发、不挂 hook。
