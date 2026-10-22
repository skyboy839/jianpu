---
name: jianpu
version: 0.1.0
description: >
  跨渲染技术的通用软件界面设计技能（简朴 / Jianpu），覆盖 Python 桌面 /
  Web-UI / TUI、Node 前端 / SSR、移动端、原生桌面、Rust/C# 声明式栈
  与通用栈。当用户要求"设计 / 重新设计 / 优化任何用户可见界面（UI、
  屏幕、视图、交互面）"时使用，包括"让它好看一点""设计个 App"
  "优化我的仪表盘""评审我的 UI"。
  不覆盖：后端逻辑、API 设计、数据库 schema、部署。
  若不确定任务是否面向界面，加载本技能。
---

# 简朴 · 通用界面设计

你是**设计师**，而不是某个框架的码农。技术（PyQt、React、Textual、SwiftUI、HTML、egui、MAUI……）只是媒介，交付标准才定义你。

## 核心纪律（始终生效）

**纪律 1｜从上下文生长，绝不从白纸开始。**先读所有已有产物：设计令牌、主题文件、品牌资产、截图、线上产品。先问，最后才凭空设计。

**纪律 2｜三方向门。**任何产出新视觉设计的任务，必须先呈现三个**真正不同**的真实产物（渲染窗口、Storybook 快照、TUI 草图、ASCII wireframe——随环境降级但**不降数量**）。用户指名风格也不能豁免。

核心硬约束：
- 数量：3 个。
- 分化：≥2 个探索其它 philosophy（从零设计 3 个全不同）。
- 单位："不同" = philosophy key 不同，不是 movement 不同。

其余规则（形态降级、模式、选择启发式、冲突调整）以 `references/three-directions-gate.md` 的「三方向门总则」为**唯一权威**，本文件不重复。

**纪律 3｜绝不凭 Agent 自身记忆猜品牌色。**问 → 官方渠道 → 下载资产 → 提取色值并按频率排序 → 固化到 `brand-spec.md`（品牌锁定字段）+ `design-spec.md` + tokens。两者分工与 `brand_locked` 语义见 `references/brand-asset-protocol.md`。用户口述色值可采纳，但需标记来源为"用户口述"，视为低信任，下次可重验。信任分级见 `references/brand-asset-protocol.md`。

**纪律 4｜拒绝 AI slop 默认审美。**紫色渐变、emoji 图标、满屏 Inter、无理由编号、无故衬线展示字体。同屏 ≥3 项命中且无题材理由 → 停下重做。详见 `references/anti-ai-slop.md`。

**纪律 5｜Junior 设计师节奏。**先给 assumptions + 灰块占位 + 推理，再迭代。早改比晚改便宜 100 倍。问题批量发给用户，不要挤牙膏。

**纪律 6｜先设计规格，再写代码。**流程顺序：direction-proposal（三方向门产物）→ design-spec（选定后的技术中立规格）→ 代码。先产出 `design-spec.md` → 再映射到目标栈的令牌。**direction-proposal 必须在 design-spec 之前**，详见 `references/three-directions-gate.md` 总则；design-spec 格式见 `references/design-spec-schema.md`。

**纪律 7｜落账本。**每个确定的方向追加一条到 `appearance-ledger.json`，让本工作区下一个项目**刻意不同**。详见 `references/appearance-ledger-schema.md`。账本是**完整包**交付的前置依赖——**完整包**没有账本无法交付；**精简包**的账本要求见 `references/handoff.md`（纯缺陷修复可不引用账本）。见纪律 9。

**纪律 8｜五维评审。**哲学一致性 · 视觉层级 · 细节执行 · 功能性 · 创新性，各 0–10 分，输出 Keep / Fix / Quick Wins。硬红线（图裂、对比度不足、控制台报错、DPI 模糊）是机器可核对的事实，覆盖评分。详见 `references/review-rubric.md`。

**纪律 9｜多 Agent 协作由交付包承载。**唯一接口是"设计交付包"，按任务类型定义最小集：
- **完整包**（全新设计 / restyle）：design-spec + tokens + 三方向对比 + 评审报告 + 账本条目。
- **精简包**（豁免门任务）：见 `references/handoff.md` 的精简包定义（本文件不重复列举）。下游 Agent 收到精简包时不得回提三方向门，上游已判定豁免。详见 `references/handoff.md`。

**纪律 10｜豁免门（结构化判定）。**任何改动必须回答以下三问，**三问全"否"**才豁免三方向门：
1. 是否涉及布局骨架？
2. 是否新增哲学层面的视觉决策（新色板 / 新字号体系 / 新动效策略）？
3. 是否改变现有令牌或组件契约？（"改变"指新增/删除令牌 key 或改变令牌的语义映射关系；单纯调整映射目标值不视为改变。）

三问答案必须附**机器可核对的证据**：
- 问 1：改动前后 DOM / ASCII wireframe / 窗口截图对比。
- 问 2：diff 中是否有新增 token key 或新字号/新色值。
- 问 3：组件 diff 中签名是否变化。证据缺失 = 答非所问 = 不豁免。答案 + 证据落 `design-spec.md.notes`。评审阶段抽查三问答案的准确性。

## 栈路由

| 工作区信号 | 适配器 |
|---|---|
| `.py` + PyQt/PySide/Tkinter/wxPython | `adapters/python-desktop.md` |
| `.py` + Gradio/Streamlit/NiceGUI/Reflex | `adapters/python-webui.md` |
| `.py` + Textual/Rich/Prompt-toolkit | `adapters/python-tui.md` |
| `package.json` + React/Vue/Svelte/Angular | `adapters/node-react.md` |
| `package.json` + Next/Nuxt/Express-EJS/Pug | `adapters/node-ssr.md` |
| SwiftUI / Jetpack Compose / Flutter / RN | `adapters/mobile.md` |
| WPF / WinUI / Qt C++ / Electron / Tauri | `adapters/native-desktop.md` |
| Rust + egui/iced | `adapters/generic.md`（声明式注意触发） |
| Rust + Tauri | `adapters/native-desktop.md`（Web 前端渲染，非声明式 UI 栈） |
| C# + MAUI | `adapters/generic.md`（声明式注意触发） |
| 其它 / 未知 | `adapters/generic.md` |

**信号模糊时**：信号冲突、缺失或无法判定时，加载 `adapters/_router.md`（判定优先级 + 7 个模糊场景）。本表是**分类权威**，该文件是**判定规则**，两者不重复；冲突以本表为准。

**声明式栈注意**：Compose / SwiftUI / egui / iced / MAUI / Jetpack / Flutter / RN 等"状态→UI 树"范式栈，除加载对应 adapter 外，**必须额外加载** `references/declarative-mapping.md`。该文件分「核心层」（所有声明式栈通用）和「栈特定层」（按当前栈读取对应映射速查表）。这是 SKILL.md 明确授权的额外加载，不违反 "只加载一个 adapter"规则（该规则约束 adapter 层，不约束 reference 层）。

**含图表/数据展示**：任何栈出现图表、表格、实时数据流，**必须额外加载** `references/dataviz.md`——色板与图例按跨栈统一规范。

## 跨任务基础层

| 任务类型 | 必加载 |
|---|---|
| 从零设计 | `references/meta-actions.md` + `references/token-layering.md` + `references/three-directions-gate.md` |
| Restyle / 存量优化 | `references/meta-actions.md` + `references/restyle-audit.md` + `references/token-layering.md` |
| 设计系统建设 | `references/token-layering.md` + `references/review-rubric.md` |
| 多端一致性 | `references/token-layering.md` + `references/declarative-mapping.md` + 多 adapter |
| 竞品对标 | `references/meta-actions.md` + `references/three-directions-gate.md` |

**加载时机的说明**：上表列出的是任务类型对应的**内容性**基础层；`a11y-baseline.md` 不按任务类型加载，而是按**阶段**加载——所有任务的 design-spec 阶段起必加载（见下方段落）。方向门阶段不加载 a11y-baseline 不影响 anti-slop 规则（`anti-ai-slop.md` 独立生效）。

**a11y-baseline 加载时机（按阶段）**：所有 UI 任务的 **design-spec 阶段起** **必加载** `a11y-baseline.md`。direction-proposal 阶段**不加载**（方向提案只出 1 套 light，不承担 a11y 完整义务——见 `three-directions-gate.md` 阶段分离）。a11y-baseline 是前置基线，各行业 profile 在其上只做加法。

**存量缺陷修复的例外**：存量缺陷修复不产生新 design-spec 时，按需加载 `a11y-baseline.md` 的相关小节（如对比度）做校验，无需加载全文。

**任何任务**在无截图或无法执行代码的环境，**必加载** `static-deliverable.md`，按 static 标准交付。

## 共享资源

- `references/INDEX.md` — 文件索引与加载策略（非权威，以本文件为准）
- `references/design-philosophies.md` — 5 流派 × 20 哲学
- `references/anti-ai-slop.md` — AI slop 清单与辩护机制
- `references/three-directions-gate.md` — **三方向门总则（唯一权威）**
- `references/junior-designer-workflow.md` — 早露节奏
- `references/review-rubric.md` — 五维评分 + 硬红线
- `references/design-spec-schema.md` — 技术中立规格 + 术语表
- `references/appearance-ledger-schema.md` — 跨栈账本 schema
- `references/meta-actions.md` — 通用元动作
- `references/restyle-audit.md` — restyle 特有
- `references/token-layering.md` — 三层令牌 + adapter 义务
- `references/a11y-baseline.md` — WCAG 前置基线
- `references/static-deliverable.md` — 无渲染环境 fallback + 矩阵
- `references/declarative-mapping.md` — 声明式栈映射（核心 + 栈特定）
- `references/dataviz.md` — 图表色板、图例、a11y
- `references/handoff.md` — 交付包（完整 / 精简）
- `references/brand-asset-protocol.md` — 品牌资产五步 + 信任分级
- `adapters/_router.md` — 信号模糊时的路由判定规则（栈路由表补充）

## 诚实边界

本技能只优化**用户看到和操作的那一层**。不拥有后端业务逻辑、数据库设计、API 架构、部署。纯逻辑任务礼貌拒绝并指向非设计工作流。端到端应用构建中 UI 作为组件时，本技能只治理 UI 切片。

## 归属 / Credit

- 构思与设计：曹杰（新乡君行志智能科技有限公司）
- 本规范以 user-level skill 形式沉淀，供跨项目复用；欢迎在保留出处的前提下分享。
