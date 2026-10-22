# 通用元动作 / Meta-Actions

本文件定义**跨任务共享的元动作**——从零设计、restyle、竞品对标都复用的部分。restyle 特有流程见 `restyle-audit.md`。

## 三个元动作

### 1. 约束提取（Constraint extraction）

**从零设计与 restyle 都执行**。

- **硬约束**：品牌色、平台规范、合规条款、已有组件库。
- **软约束**：用户偏好、团队审美倾向、可复用的 API。
- **区分二者**——硬约束不可变，软约束可谈。

硬约束落 `design-spec.md.constraints.hard`，软约束落 `design-spec.md.constraints.soft`。

### 2. 方向选择（Direction selection）

**从零设计与 restyle 都执行**。

三方向门阶段的"选哪三个"启发式定义在`three-directions-gate.md`。本文件不重复。

关键点：约束提取的结果（步骤 1）直接输入方向选择启发式的步骤 1（约束推导）。两个元动作顺序衔接。

### 3. 渐进迁移的通用层次原则

**从零设计与 restyle 都执行。**

无论从零还是 restyle，实现都按以下**层次顺序**推进：
1. 布局骨架（灰块占位）。
2. 视觉风格（色、字、圆角）。
3. 交互细节（动效、状态）。
4. a11y 完整检查。

**每次推进都要 show**。早改比晚改便宜 100 倍（junior-designer workflow）。

本元动作只定义**通用层次顺序**。restyle 特有的"风险分级"和 "每 PR 一个 surface"纪律见 `restyle-audit.md` 步骤 4——它们在通用层次之上叠加，不替代。

## restyle 特有什么

以下**只有 restyle 有**，见 `restyle-audit.md`：
- 现状审计（截图、导出、现有令牌提取）。
- 迁移顺序（低风险→中风险→高风险的具体分级）。
- before/after 对照。

## 与三方向门的关系

元动作产出三方向门的**输入**（3 个候选 philosophy）。三方向门本身在 `three-directions-gate.md`。本文件不重复三方向门规则。

## 与 token-layering 的关系

约束提取阶段产出的现有令牌 → 直接进入 `token-layering.md` 的 primitive 层。迁移阶段的新语义 → 进入 semantic 层。组件契约 → component 层。
