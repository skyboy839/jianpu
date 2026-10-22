# INDEX — 文件索引与加载策略

**任务加载表以 `../SKILL.md` 的「跨任务基础层」表为唯一权威。**本文件不重复任务表——避免两处定义导致不同步。

## 文件索引

| 文件 | 用途 |
|---|---|
| `design-philosophies.md` | 5 流派 × 20 哲学，约束 + 反模式形式 |
| `anti-ai-slop.md` | AI slop 清单 + 辩护机制 |
| `three-directions-gate.md` | **三方向门总则（唯一权威）** |
| `junior-designer-workflow.md` | 早露节奏 |
| `review-rubric.md` | 五维评分 + 硬红线 |
| `design-spec-schema.md` | 技术中立规格 + 术语表 |
| `appearance-ledger-schema.md` | 跨栈账本 schema |
| `meta-actions.md` | 通用元动作（约束提取 / 方向选择 / 渐进迁移） |
| `restyle-audit.md` | restyle 特有（现状审计 + 迁移顺序） |
| `token-layering.md` | 三层令牌 + adapter 义务 |
| `a11y-baseline.md` | WCAG 前置基线 |
| `static-deliverable.md` | 无渲染环境 fallback + 可表达性矩阵 |
| `declarative-mapping.md` | 声明式栈映射（核心层 + 栈特定层） |
| `dataviz.md` | 图表色板、图例、a11y |
| `handoff.md` | 交付包（完整 / 精简） |
| `brand-asset-protocol.md` | 品牌资产五步 + 信任分级 |

## 硬性加载（特定条件下）

- `a11y-baseline.md`：**design-spec 阶段起**必加载（判定规则见 SKILL.md）。
- `static-deliverable.md`：无截图或无法执行代码的环境。
- `declarative-mapping.md`：Compose / SwiftUI / egui / iced / MAUI / Jetpack / Flutter / RN 栈。
- `dataviz.md`：任何图表、表格、实时数据流。
- `handoff.md`：多 Agent 协作时。

## 建议不加载（节省上下文）

- `design-philosophies.md` 全 20 种：只加载候选方向涉及的 3–5 种。
- `review-rubric.md` 用于任务中途：只在交付前加载。
- `appearance-ledger-schema.md`：只在落账本或读取最近 3 条时加载。
- `brand-asset-protocol.md`：任务不涉及品牌资产时无需加载。

## 加载策略的权威顺序

1. SKILL.md 的「跨任务基础层」表——**唯一权威**。
2. 本文件的「硬性加载」「建议不加载」——补充说明，不与 SKILL.md 冲突。
3. 若本文件与 SKILL.md 冲突，以 SKILL.md 为准，并应提交修复。
