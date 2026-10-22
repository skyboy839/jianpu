# design-spec.md — 技术中立设计规格

在写代码**之前**产出。每个 adapter 读取本文件生成令牌。

## 术语表（唯一权威定义）

| 术语 | 定义 | 产物要求 |
|---|---|---|
| **direction-proposal** | 三方向门阶段的产物（3 个 philosophy 不同的真实产物），只出 1 套模式（默认 light），不含完整 tokens | 3 个可运行/可静态呈现的最小产物 |
| **design-spec** | 方向选定后的技术中立规格，含 light/dark 成对色板 | 完整规格 + tokens 映射 |
| **direction** | 最终选定的那一支（1 个） | 落地为 design-spec |
| **brand_locked** | 品牌方强制规定的字段（如 accent、bg） | 传递到账本，跳过差异检查 |
| **philosophy key** | design-philosophies.md 中的唯一键（如 `minimalism`） | "不同"的比较单位 |
| **movement** | 5 流派之一（信息建筑 / 极简 / 运动诗学 / 实验先锋 / 东方） | philosophy key 的上层分类 |
| **`radius.md`** | radius 尺寸的 **medium 档**（`sm` / `md` / `lg` / `full` 之一，值 8）；`.md` 是 medium 缩写，**不是** Markdown 文件扩展名 | 映射到目标栈的圆角令牌 |

**direction-proposal vs design-spec**：前者是 3 选 1 的过程产物，后者是选定后的实现规格。前者不承担 a11y 完整义务，后者承担。

## 结构

```yaml
# design-spec.md
philosophy: minimalism              # 一个 philosophy key
movement: minimal                   # 用于反单调交叉检查
subject: "internal monitoring tool for on-call engineers"

justification:
  - "information density prioritized: ≥8 metrics per screen"
  - "no decorative icons: on-call context rewards signal only"

constraints:
  hard:
    - "brand accent fixed by brand-spec.md 2.1.0"
    - "WCAG AAA (healthcare adjacent)"
  soft:
    - "team prefers dark UI"
    - "reuse existing Chart component"

colors:
  # 成对产出（light / dark），符合 a11y-baseline 强制
  bg:
    light: oklch(0.98 0.005 250)   # #F8F9FA
    dark:  oklch(0.15 0.005 250)
  surface:
    light: oklch(0.96 0.008 250)
    dark:  oklch(0.20 0.008 250)
  text:
    light: oklch(0.20 0.010 250)
    dark:  oklch(0.92 0.010 250)
  muted:
    light: oklch(0.55 0.010 250)
    dark:  oklch(0.65 0.010 250)
  accent:
    light: oklch(0.62 0.16 30)     # brand locked
    dark:  oklch(0.70 0.16 30)     # brand locked (adjusted for dark bg)
  danger:
    light: oklch(0.58 0.18 25)
    dark:  oklch(0.65 0.18 25)
  success:
    light: oklch(0.62 0.14 150)
    dark:  oklch(0.70 0.14 150)
  contrast_checked:
    - ["text.light", "bg.light", "AAA body 7:1"]
    - ["text.dark",  "bg.dark",  "AAA body 7:1"]
    - ["accent.light", "bg.light", "AA large 3:1"]
    - ["accent.dark",  "bg.dark",  "AA large 3:1"]
  dataviz:                          # 若有图表必填
    categorical: { light: ["oklch(...)", ...], dark: ["oklch(...)", ...] }
    sequential:  { light: ["oklch(...)", ...], dark: ["oklch(...)", ...] }
    diverging:   { light: ["oklch(...)", ...], dark: ["oklch(...)", ...] }

typography:
  family_ui:   "IBM Plex Sans, system-ui"
  family_mono: "IBM Plex Mono, ui-monospace"
  scale:       [12, 13, 14, 16, 20, 24, 32]   # px
  line_height: [1.4, 1.45, 1.5]
  weights:     [400, 500, 600]

spacing:
  unit: 4
  scale: [4, 8, 12, 16, 24, 32, 48, 64]

radius:
  sm: 4
  md: 8
  lg: 12
  full: 9999

motion:
  easing:        cubic-bezier(0.2, 0.0, 0.0, 1.0)
  duration_fast: 120ms
  duration_base: 200ms
  duration_slow: 320ms
  principles:    ["state-encoding only", "no ambient loops"]

brand:
  source: "brand-spec.md §2.1.0 (trusted, ≤30d)"
  brand_locked: ["accent", "bg"]
  assets_frozen_in: "brand/"

a11y:
  wcag_level: AAA
  reduced_motion: supported
  dark_mode: paired               # design-spec 阶段必须成对

notes:
  - "Deliberately avoided purple gradient (anti-ai-slop #1)"
  - "Direction gate: direction-proposal -> design-spec, light+dark paired"
  - "exemption_gate (if applicable): see discipline 10 evidence"
```

## 规则

- 每个 anti-slop 命中必须在 `justification` 有对应条目。
- 每个 `colors` token 用于代码的必须在此存在。代码中无孤儿 hex。
- 用户提供品牌 kit，`brand.source` 必须指向协议输出；提取强制，记忆禁止。
- `a11y.wcag_level` 必须显式声明。
- `a11y.dark_mode: paired` 是默认（design-spec 阶段成对）。
- 若 static 环境，`notes` 需追加 `static_deliverable: true` 和使用的可表达性矩阵版本。

## 与 direction-proposal 的界限

- direction-proposal 阶段产出的是 3 个候选方案，**不是** design-spec。
- 每个 direction-proposal 只需 1 套模式（默认 light），不承担 a11y 完整义务。
- design-spec 是方向**选定后**的产物，必须成对 light/dark、完整 a11y 检查。
