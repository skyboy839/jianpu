# 声明式栈共性映射 / Declarative Mapping

## 适用栈

Compose · SwiftUI · egui · iced · MAUI · Jetpack · **Flutter** · **RN** · 以及任何 "State → UI Tree" 范式的栈。

**Flutter / RN 说明**：Flutter（widget 树 + ThemeData）与 RN （props + StyleSheet）的具体 API 与 Compose/SwiftUI 差异较大，但同属"状态驱动视图树"范式。本文件的**核心层**（4 层模型、反模式、方向门载体）对它们同样适用；**栈特定层**给出各自的映射速查表。

## 环境能力标签

按 SKILL.md 路由规则加载。若本栈为 `static-only`，按 `static-deliverable.md` 矩阵执行三方向门。

---

## 第一部分：核心层（所有声明式栈通用）

### 统一映射模型

```text
State  →  UI Tree  →  Attributes  →  Tokens
(数据)   (结构)      (属性)          (值)
```

**层 1｜State → UI Tree**
- State 变化的可预测性 = 交互质量根基。
- 每个 UI Tree 节点必须能被一个明确 state 解释"为什么这样渲染"。
- 反模式：同一节点被多个 state 隐式驱动而无优先级规则。

**层 2｜UI Tree → Attributes**
- 属性分三类：
  - 布局属性：参照 design-spec 的 `spacing` / `radius` / `layout`。
  - 样式属性：参照 `semantic` token 层。
  - 行为属性：交互、动效、a11y。

**层 3｜Attributes → Tokens**
- 属性值不得硬编码，全部引用 token。
- 各栈的 token 挂载方式见**第二部分**的栈特定小节。

### 声明式栈特有反模式

- 把 state 逻辑写进 UI 树（应上提）。
- 用平台默认主题直接发布（等于没做设计）。
- 组件内硬编码值（不引用 token）。
- 忽视 preview / Golden Test（三方向门的最佳载体）。
- 忽视无障碍语义树（`semantics {}` / `accessibilityLabel`）。

### 三方向门的实现载体

| 栈 | 三方向交付形态 |
|---|---|
| Compose | 三个 `@Preview` 函数 |
| SwiftUI | 三个 `#Preview` |
| egui | 三个 `fn ui_demo_x` |
| MAUI | 三个 XAML 布局文件 |
| Flutter | 三个 Widget 页面 + Golden Test 快照 |
| RN | 三个 Screen + Storybook stories |

默认假设有 preview 能力；若无，按 `static-deliverable.md` 降级。

---

## 第二部分：栈特定层（按当前栈读取对应小节）

### Compose（Jetpack / Compose Multiplatform）

| design-spec 字段 | Compose API |
|---|---|
| `color.bg` | `MaterialTheme.colorScheme.background` |
| `font.ui` | `Typography.bodyLarge` |
| `spacing.4` | `8.dp`（`Arrangement.spacedBy` / `Modifier.padding`） |
| `radius.md` | `RoundedCornerShape(8.dp)` |
| `motion.easing` | `tween(easing = FastOutSlowInEasing)` |

### SwiftUI

| design-spec 字段 | SwiftUI API |
|---|---|
| `color.bg` | `Color("bg")`（自定义 extension） |
| `font.ui` | `.font(.body)` |
| `spacing.4` | `.padding(8)` |
| `radius.md` | `.cornerRadius(8)` |
| `motion.easing` | `.animation(.easeInOut)` |

### egui（Rust）

| design-spec 字段 | egui API |
|---|---|
| `color.bg` | `Visuals.panel_fill` |
| `font.ui` | `TextStyle::Body` |
| `spacing.4` | `ui.spacing.item_spacing` |
| `radius.md` | `Rounding { nw: 8.0, .. }` |
| `motion.easing` | `ctx.animate_bool_with_time` |

### MAUI（C#）

| design-spec 字段 | MAUI API |
|---|---|
| `color.bg` | `{DynamicResource BgColor}` |
| `font.ui` | `{StaticResource UiFont}` |
| `spacing.4` | `Margin="8"` |
| `radius.md` | `CornerRadius="8"` |
| `motion.easing` | `.Animate` |

### Flutter

| design-spec 字段 | Flutter API |
|---|---|
| `color.bg` | `Theme.of(context).colorScheme.background` |
| `font.ui` | `Theme.of(context).textTheme.bodyLarge` |
| `spacing.4` | `SizedBox(height: 16)` / `EdgeInsets.all(16)` / `gap: 16` |
| `radius.md` | `BorderRadius.circular(8)` |
| `motion.easing` | `Curves.easeOutCubic` |

**Flutter 特有**：`ThemeData` 是 token 挂载点（`MaterialApp(theme: ...)`）；`ColorScheme` 通过 `ColorScheme.fromSeed` 或显式构造，从 semantic tokens 生成。**Golden Test**（`flutter test --update-goldens`）是三方向门的最佳载体。

### RN（React Native）

| design-spec 字段 | RN API |
|---|---|
| `color.bg` | `useColorScheme()` + theme context（如 `react-native-paper` 的 `MD3Theme`） |
| `font.ui` | `Text style={styles.body}` |
| `spacing.4` | `margin: 16` / `padding: 16` / `gap: 16` |
| `radius.md` | `borderRadius: 8` |
| `motion.easing` | `Animated.timing` 的 `easing: Easing.out(Easing.cubic)` |

**RN 特有**：`StyleSheet.create` 是样式集中点；`react-native-paper` / `react-native-ui-lib` 提供 theme provider。**Storybook for RN** 是三方向门的最佳载体。

---

## 与 token-layering.md 的关系

各栈的 `theme` 三层文件结构见 `token-layering.md` 的「adapter 三层结构义务」表。本文件只定义**映射速查**，不定义文件结构。
