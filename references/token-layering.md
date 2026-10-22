# 令牌分层 / Token Layering

## 三层模型

### 1. Primitive（原语层）

- 物理值：具体色相、字号、间距、圆角。
- 不携带业务语义。
- 例如：`color.blue.500 = oklch(0.62 0.16 250)`，`space.4 = 16px`。

### 2. Semantic（语义层）

- 引用 primitive 并赋予意义。
- 例如：`color.text.primary = color.blue.900`，`color.action.primary = color.blue.500`。
- **跨栈通用语言**——Python 桌面、React、Textual 都指向同一组语义名，仅实现映射不同。

### 3. Component（组件层）

- 引用 semantic 并组合为组件属性。
- 例如：`button.primary.bg = color.action.primary`，`button.primary.radius = radius.md`。
- 组件 API 契约由此生成（不给组件直接传 primitive）。

## 契约纪律

- 组件**不得**直接引用 primitive。只能引用 semantic 或 component。
- 改 primitive 只影响颜色/字号本身；改 semantic 影响所有引用方——这是版本化的风险点。
- 跨栈复用时，primitive 和 semantic 层同步，component 层可各自实现（Qt 的按钮 ≠ React 的按钮）。

## adapter 三层结构义务

**每个 adapter 必须给出 `theme` 的三层文件结构**：

| 栈 | 三层文件 |
|---|---|
| Python（PyQt/PySide/Tkinter/wxPython/Gradio/Streamlit/NiceGUI/Reflex） | `primitives.py` / `semantic.py` / `components.py` |
| Python TUI（Textual/Rich/Prompt-toolkit） | `tokens/primitive.tcss` / `semantic.tcss` / `components.tcss` |
| React / Vue / Svelte / Angular | `tokens.primitive.ts` / `tokens.semantic.ts` / `theme.components.ts` |
| Node SSR（Next/Nuxt/Express-EJS/Pug） | `tokens/primitive.ts` / `tokens.semantic.ts` / `theme/components.ts`（服务端渲染引用 CSS 变量） |
| SwiftUI | `Primitives.swift` / `Semantic.swift` / `Components.swift` |
| Compose（Jetpack / Multiplatform） | `Primitives.kt` / `Semantic.kt` / `Components.kt` |
| egui / iced（Rust） | `primitives.rs` / `semantic.rs` / `components.rs` |
| MAUI（C#） | `Primitives.xaml` / `Semantic.xaml` / `Components.xaml` |
| Flutter | `lib/tokens/primitives.dart` / `semantic.dart` / `components.dart` |
| RN（React Native） | `tokens/primitive.ts` / `tokens.semantic.ts` / `theme/components.ts` |
| 原生桌面：WPF / WinUI | `Primitives.xaml` / `Semantic.xaml` / `Components.xaml` |
| 原生桌面：Qt C++ | `primitives.h` / `semantic.h` / `components.h` |
| 原生桌面：Electron / Tauri | `tokens.primitive.ts` / `tokens.semantic.ts` / `theme.components.ts` |

**adapter 只写映射，不写结构**。widget 只从 `components.*` 导入，**不得**直接从 `primitives.*` 导入。审计阶段检查此项——发现 widget 直接引用 primitive 即为违规。

## 版本化

- primitive：随品牌更新，major bump。
- semantic：随语义调整，minor bump。
- component：随组件 API 调整，patch bump。
- 每次 bump 记录到 `appearance-ledger.json` 的对应字段（`primitive_bump` / `semantic_bump` / `component_bump`）。

## 与 design-spec 的关系

`design-spec.md` 是人类可读的意图声明；`tokens` 是机器可读的实现。两者必须**互相对应**——design-spec 的每个声明在 tokens 有落点，tokens 的每个值在 design-spec 有理由。

## 与 brand_locked 的关系

`brand_locked` 的锁定层级（primitive 层）见 `brand-asset-protocol.md` 的「品牌锁定」小节。

对分层的实际影响：semantic 层可自由映射到已锁定的 primitive 或未锁定的 primitive——保留设计自由度，同时不违反品牌约束。
