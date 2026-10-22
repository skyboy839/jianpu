# Adapter: 移动端（SwiftUI / Jetpack Compose / Flutter / RN）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 声明式栈加载提示

本 adapter 覆盖的 **Compose / SwiftUI / Flutter / RN 全部为声明式栈**。除本 adapter 外，**必须额外加载** `../references/declarative-mapping.md`（SKILL.md 明确授权）。该文件分「核心层」（所有声明式栈通用）和「栈特定层」（按当前栈读取对应映射速查表）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- SwiftUI：`Primitives.swift` / `Semantic.swift` / `Components.swift`
- Compose：`Primitives.kt` / `Semantic.kt` / `Components.kt`
- Flutter：`lib/tokens/primitives.dart` / `semantic.dart` / `components.dart`
- RN：`tokens/primitive.ts` / `tokens.semantic.ts` / `theme/components.ts`

组件只从 components 层导入，不得直接引用 primitives。

## design-spec 映射

各栈的完整映射速查表见 `../references/declarative-mapping.md` 的「栈特定层」。本文件只补充移动端特有的约束。

| design-spec 字段 | 移动端通用要点 |
|---|---|
| color tokens | 必须 light/dark 成对；系统暗色跟随 |
| type scale | 必须响应系统字号缩放（Dynamic Type / fontScale） |
| spacing scale | 遵守安全区（safe area / inset） |
| radius | 平台惯例（iOS 连续圆角 vs Android 圆角） |
| motion | 尊重系统「减少动态效果」 |

## 三方向门——如何呈现真实方向

| 栈 | 三方向交付形态 |
|---|---|
| SwiftUI | 3 个 `#Preview`（Xcode Previews） |
| Compose | 3 个 `@Preview`（Android Studio Preview） |
| Flutter | 3 个 Widget 页面 + Golden Test 快照 |
| RN | 3 个 Screen + Storybook stories |

- capture 环境用各平台的截图工具或模拟器抓图。
- static 环境按 `../references/static-deliverable.md` 降级为 ASCII + 令牌表。
- 移动端三方向必须在**真实设备尺寸**下呈现（如 iPhone 15 / Pixel 7 视口），不要用桌面拉伸模拟。

## 栈特定评审检查

- **系统字号缩放**：在最大系统字号下验证，不得截断或布局崩坏。
- **安全区**：刘海/挖孔/手势条区域不被内容遮挡。
- **暗色模式**：跟随系统，成对色板，独立检查暗色对比度。
- **触控目标**：≥44×44 pt（iOS）/ ≥48×48 dp（Android）。
- **减少动态效果**：关闭后信息不丢失。
- **横竖屏**：至少验证一次旋转后的布局。
- **键盘弹出**：输入框不被键盘遮挡，可滚动到可见。
- **无障碍语义**：`accessibilityLabel` / `contentDescription` / `semantics`。
- **性能**：长列表用懒加载（LazyList / FlatList / ListView.builder）。

## 栈特定反模式

- 忽略系统字号缩放（用固定像素字号）。
- 忽略安全区（内容压到刘海/手势条下）。
- 用桌面拉伸模拟移动端（未验证真实视口）。
- 暗色模式简单反转浅色（对比度失效）。
- 硬编码颜色/尺寸（不引用令牌）。
- 长列表不做懒加载（滚动卡顿 + 内存暴涨）。
- 触控目标小于平台最小尺寸。
- 用平台默认主题直接发布（等于没做设计）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| 固定字号 | 大字号用户无法阅读 | 用 Dynamic Type / fontScale |
| 忽略 safe area | 刘海遮挡内容 | 使用安全区容器 |
| 列表全量渲染 | 卡顿/内存溢出 | 懒加载 |
| 暗色简单反转 | 对比度不达标 | 独立检查暗色 |
| 键盘遮挡输入框 | 无法输入 | 滚动到可见 + 键盘避让 |
| 触控目标过小 | 误触 | ≥44/48 平台最小 |
| 硬编码平台尺寸 | 适配多设备失败 | 用 dp/pt 与响应式布局 |
