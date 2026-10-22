# Restyle 审计 / Restyle Audit

本文件定义 **restyle 特有**流程。通用元动作（约束提取、方向选择、渐进迁移）见 `meta-actions.md`。三方向门规则见 `three-directions-gate.md`。

## 四步流程（restyle 特有部分）

### 1. 现状审计

- 截图或导出当前所有主要界面（每 surface ≥1 张）。
- 提取现有色板、字号、间距、圆角到 `audit.json`。
- 记录现有 philosophy（若明确）→ 若无，标 `unknown`。
- 列出 5–10 个"用户痛点"（非审美），标注来源（日志 / 访谈 / 直觉）。

### 2. 约束提取

见 `meta-actions.md` 元动作 1。

### 3. 三方向门

见 `three-directions-gate.md`。

restyle 场景允许一个方向为"当前 philosophy 增强"（在`three-directions-gate.md` 中已定义为允许的情形）。

### 4. 渐进迁移

**在 `meta-actions.md` 元动作 3 的通用层次顺序之上叠加 restyle 特有纪律**：

- **每个 PR 只改一个 surface**（通用层次允许单 PR 跨越多个层次，restyle 限制单 PR 单 surface）。
- **风险分级**：低风险（纯视觉）→ 中风险（组件 API 微调）→高风险（布局重排）。
- 每个 PR 附：before / after 截图 + 令牌差异 + 回归测试清单。
- 落地账本：每个 surface 完成后记录进 `appearance-ledger.json`。

**关系澄清**：风险分级是 restyle 对迁移**顺序**的细化（按风险排序）；通用层次顺序是对单次迁移**内部**的细化（按层次排序）。两者正交，共同执行——每个 PR 内按层次推进，PR 之间按风险排序。

## 迁移顺序分级

| 风险级 | 改动内容 | 前置检查 |
|---|---|---|
| 低 | 纯视觉（色、字、圆角）不改结构 | 令牌审计完成 |
| 中 | 组件 API 微调（不破坏现有调用） | 全量测试通过 |
| 高 | 布局重排（改动 DOM 树 / 控件层级） | 三方向门选定 + 回归清单 |

## 反模式

- 一次性重写所有 UI（无渐进、无对照）。
- 只做审美替换，不做约束提取 → 复发。
- 声称"从零设计"但实际在做 restyle（浪费三方向门预算）。

## 与 meta-actions 的配合

- 步骤 2（约束提取）委托给 `meta-actions.md`。
- 步骤 3（三方向门）委托给 `three-directions-gate.md`。
- 步骤 4 的"渐进迁移"叠加在 `meta-actions.md` 元动作 3 的通用层次顺序之上，本文件补充"风险分级"和"每 PR 一个 surface"细节。
