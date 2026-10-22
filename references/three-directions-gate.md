# 三方向门 / Three-Directions Gate

## 三方向门总则（唯一权威）

本小节是三方向门的**唯一权威定义**。其他文件（`static-deliverable.md`、`a11y-baseline.md`、`meta-actions.md`）**只引用，不重复**。

```text
任何新视觉设计任务：
1. 数量：3 个。
2. 分化：≥2 个探索其它 philosophy（从零设计 3 个全不同）。
3. 单位："不同" = philosophy key 不同，不是 movement 不同。
   同一 movement 下不同 philosophy 也算不同。
4. 形态：随环境降级——
   - 可运行环境：3 个可运行真实产物（窗口/Storybook/TUI 草图）。
   - 截图环境：3 个 PNG/快照。
   - static 环境：按 static-deliverable.md 的《哲学可表达性矩阵》，
     最低要求 ≥2 个方向在布局上真分化，第三个可标注"渲染后验证"。
5. 模式：direction-proposal 阶段只出 1 套（默认 light）。
   light/dark 成对义务在 design-spec 阶段补齐。
6. 选择：按下方"选择启发式"（建议，须满足第 2 条硬约束）。
```

## 何时触发

任何**产出新视觉设计**的任务：
- 新 App / 新页面 / 新视图。
- Restyle 或"让它好看"但未指名方向。
- 品牌资产应用到新 surface。

**不触发**（见 SKILL.md 纪律 10 的三问豁免门）：
- UI 缺陷修复、保现有外观的 bug fix。
- A/B 已定方向内的迭代。

## 为什么存在

盲选实验显示：给出真实选项（可见样张 + 更多候选）比给出禁令更能提升设计质量。这是质量机制，不是礼貌仪式。

## "真实"在各栈中的定义

| 栈 | "真实"产物 |
|---|---|
| PyQt / Tkinter | 3 个可运行窗口 + `grab().save()` PNG |
| React / Vue | 3 个路由或 3 个 Storybook stories（1440×900） |
| Gradio / Streamlit | 3 个 `demo_X.py`，均可运行 |
| Textual | 3 个 `textual run` 捕获（PNG 或 ANSI dump） |
| Compose | 3 个 `@Preview` 函数 |
| SwiftUI | 3 个 `#Preview` |
| egui | 3 个 `fn ui_demo_x` |
| MAUI | 3 个 XAML 布局文件 |
| Flutter | 3 个 Widget 页面 + Golden Test 快照 |
| RN | 3 个 Screen + Storybook stories |
| Generic / static | ASCII wireframe + 令牌表 + 截图占位 |

## 选哪三个（选择启发式）

从 20 种 philosophy 中选 3 个，按以下 4 步：

**步骤 1｜约束推导。**从 `meta-actions.md` 的约束提取结果出发——约束隐含哲学倾向：
- 高信息密度 → 信息建筑派
- 克制 / 留白 → 极简主义派
- 沉浸 / 实时 → 运动诗学派
- 温润 / 阅读 → 东方哲学派
- 实验 / 张力 → 实验先锋派

**步骤 2｜用户关键词映射。**
| 用户原话关键词 | 哲学候选 |
|---|---|
| "专业 / 效率 / 数据" | 信息建筑派 |
| "极简 / 干净 / 克制" | 极简主义派 |
| "动感 / 沉浸 / 实时" | 运动诗学派 |
| "温润 / 诗意 / 阅读" | 东方哲学派 |
| "先锋 / 实验 / 张力" | 实验先锋派 |

**步骤 3｜账本反排除。**从账本 `rejected_directions` 反向排除上一项目已否定过的方向。

**步骤 4｜轮转兜底。**无信号时，从 5 个 movement 各取一个代表，按账本最近项目反向选 3 个（优先选上项目未用过的 movement）。

## 启发式冲突调整（当启发式与硬约束冲突）

若启发式输出不满足硬约束（从零设计三全不同 / restyle 至少两个探索其它），按以下顺序调整：

1. **优先保留**用户关键词命中的那个 philosophy。
2. **优先替换**最"重复"的 philosophy（与保留者同 movement 的）。
3. 替换候选从账本 `rejected_directions` 以外、未使用过的 movement 中选。
4. 若 5 个 movement 已用尽仍不满足，说明候选池不足，**报告用户** "约束内无法产出 3 个不同方向"，请用户放宽约束。不得静默降级。

## "不同"的单位澄清

**不同 = philosophy key 不同**，不是 movement 不同。
- 信息建筑派（理性）和极简主义派（理性）虽然同属理性系，但 philosophy key 不同 → **算不同**。
- 信息建筑派的两种变体（如"数据仪表"vs"控制台"）若 philosophy key 相同 → **不算不同**。

## 流程

1. 读上下文（tokens、代码库、截图）→ 1 行摘要给用户。
2. 按"选哪三个"启发式选出 3 个 philosophy。
3. 按栈 adapter 规范产出 3 个 direction-proposal（只 1 套模式）。
4. **在询问用户前**同时呈现 3 个方向。
5. 用户选 1（或重组）。记录选择 + 被拒原因进 `appearance-ledger.json`。
6. 进入 `design-spec` 阶段：补齐 light/dark 成对色板。
7. 进入完整实现。

## 失败模式

- "我只做一个方向问好不好" → 违规。
- "我用文字描述三个方向" → 违规，必须可视/可运行。
- "用户说暗色模式" → 仍触发，暗色是参数，不是 philosophy。
- "跳过 light/dark" → 分阶段是允许的，但 design-spec 阶段必须补齐。
- "static 环境降级为文字描述" → 违规，static 交付必须按 static-deliverable.md 的矩阵执行。

## 阶段分离（本小节定义阶段，其余文件引用）

三方向门阶段的产物是 **direction-proposal**（不是 design-spec）：
- 只出 1 套模式（默认 light）。
- 不承担 light/dark 成对义务。
- 不承担完整 a11y 检查义务。

方向选定后才进入 design-spec 阶段，补齐 light/dark 成对 + a11y 完整检查。术语定义见 `design-spec-schema.md` 的术语表。
