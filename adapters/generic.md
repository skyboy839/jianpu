# Adapter: 通用 / 未知栈

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 已知声明式栈清单

以下"State → UI Tree"范式的栈加载本文件时**自动加载** `../references/declarative-mapping.md`（SKILL.md 明确授权）：

- Jetpack Compose（Kotlin）
- SwiftUI（Swift）
- egui（Rust）
- iced（Rust）
- MAUI（C#）
- Jetpack（Android）
- Flutter（Dart）
- RN（React Native）
- 任何其他"State → UI Tree"范式栈

**这是 SKILL.md 明确授权的额外加载**，不违反"只加载一个 adapter" 规则（该规则约束 adapter 层，不约束 reference 层）。

## 适用场景

本 adapter 覆盖以下情况：

1. **工作区信号未知**——无法从文件扩展名、依赖清单、入口文件判定栈类型。
2. **Rust + egui / iced**——声明式栈，走本文件 + 自动加载 `../references/declarative-mapping.md`。
3. **C# + MAUI**——声明式栈，走本文件 + 自动加载 `../references/declarative-mapping.md`。
4. **其它小众或自研 UI 框架**——无专用 adapter 时的通用兜底。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件**按实际栈推断**：

- 若命中已知声明式栈清单 → 用 `../references/token-layering.md` 表格中对应行。
- 若为未知栈 → 按目标语言惯例建立 `primitives.*` / `semantic.*` / `components.*` 三个文件，并在 `design-spec.md.notes` 记录推断依据。

组件只从 components 层导入，不得直接引用 primitives。

## 通用映射

目标栈未知时：

1. **读现有令牌**——工作区若有 `tokens.*` / `theme.*` / 样式变量文件，提取为 primitive 层。
2. **无现有令牌**——从 `design-spec.md` 生成三层结构。
3. **按 token-layering.md 的三层结构落地**。
4. **三方向门产物按实际环境能力降级**（runnable → static）。

## 三方向门——交付形态

| 环境 | 产物形态 |
|---|---|
| runnable + capture-capable | 3 个可运行产物 + PNG |
| runnable + capture-disabled | 3 个可运行产物 + ASCII 描述 |
| static-only | 按 `../references/static-deliverable.md` 矩阵执行 |

**未知栈的三方向门特别注意**：由于无法调用栈特定的 preview / 截图工具，优先确认环境能力标签。若 `capture-disabled`，**直接用 ASCII wireframe + 令牌表**，不要假装有可视化。

## 评审检查（通用）

- **对比度**：正文 ≥4.5:1，大字与 UI 组件 ≥3:1（见 a11y-baseline）。
- **焦点可见**：每个交互元素有可见焦点态。
- **键盘路径**：所有操作可键盘完成。
- **长字符串**：截断有省略号，不无声溢出。
- **边界数据**：空、1 条、1 万条各验证一次。
- **DPI / 缩放**：若为桌面或移动环境，验证缩放不模糊。
- **暗色模式**：若支持，成对色板，独立检查对比度。
- **线程安全**：UI 更新在主线程（若适用）。

## 通用反模式

- 用通用占位组件（如 Bootstrap 默认按钮、Qt 默认控件）直接发布。
- 硬编码颜色 / 尺寸（不引用令牌）。
- 无视目标平台的惯例（如 Android 用 iOS 返回手势、桌面应用移除焦点环）。
- 未知栈上猜 adapter 而不问用户。
- 在 static 环境假装有截图。
- 混用多个 UI 框架而不显式声明是多端任务。

## 未知栈的判定流程

```text
1. 用户原话能否判定渲染层？
   ├── 是 → 按用户原话路由到具体 adapter
   └── 否 → 继续
2. 依赖清单 / 入口文件能否判定？
   ├── 是 → 按 SKILL.md 路由表路由
   └── 否 → 继续
3. 命中「已知声明式栈清单」？
   ├── 是 → 本文件 + declarative-mapping.md
   └── 否 → 继续
4. 问用户一个封闭问题：
   "这是桌面窗口、网页、终端界面，还是移动应用？"
   根据回答重新路由。
```

**不在未知栈上猜测后直接产出**——猜错的成本高于多问一句。

## 与 _router.md 的关系

`_router.md` 定义**判定优先级**和**模糊场景处理**；本文件是**未知栈的兜底 adapter**。两者配合：router 决定"走哪个 adapter"，generic 是"没有专用 adapter 时"的落点。

## 反模式（路由层面）

- 在未知栈上硬猜一个具体 adapter（应该走 generic + 问用户）。
- 同时加载多个 adapter 而不声明是多端任务。
- 忽略用户原话，只靠文件扫描路由。
