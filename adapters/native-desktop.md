# Adapter: 原生桌面（WPF / WinUI / Qt C++ / Electron / Tauri）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- WPF / WinUI：`Primitives.xaml` / `Semantic.xaml` / `Components.xaml`（资源字典）
- Qt C++：`primitives.h` / `semantic.h` / `components.h`
- Electron / Tauri：`tokens.primitive.ts` / `tokens.semantic.ts` / `theme.components.ts`

视图/组件只从 components 层引用，不得直接写 primitive 值。

## design-spec 映射

| design-spec 字段 | WPF / WinUI | Qt C++ | Electron / Tauri |
|---|---|---|---|
| color tokens | `ResourceDictionary` 中的 `Color`/`SolidColorBrush` | `QPalette` + QSS 变量 | CSS 自定义属性 |
| type scale | `FontSize` 资源 | `QFont` + QSS `font-size` | CSS `font-size` |
| spacing scale | `Thickness` 资源 | `setSpacing` / `setContentsMargins` | CSS `gap`/`padding` |
| radius | `CornerRadius` | QSS `border-radius` | CSS `border-radius` |
| motion | Storyboard + 缓动资源 | `QPropertyAnimation` | CSS transition + easing token |

**Electron / Tauri 特别说明**：UI 由 Web 前端渲染，但它是**桌面应用**而非"Node 前端"——设计约束以宿主窗口为准（DPI、焦点、系统主题、菜单栏），网页内部的 CSS 是次要媒介。路由表已明确。

## 三方向门——如何呈现真实方向

| 栈 | 三方向交付形态 |
|---|---|
| WPF / WinUI | 3 个窗口（`Window`）+ XAML 设计时预览截图 |
| Qt C++ | 3 个可运行窗口 + `QWidget::grab().save()` PNG |
| Electron | 3 个路由窗口 + Playwright 截图 |
| Tauri | 3 个路由窗口 + WebView 截图 |

- 每个方向 ≤80 行（或等价最小窗口），一屏，真实令牌。
- 不用文字描述方向；三张截图并排呈现。
- static 环境按 `../references/static-deliverable.md` 降级。

## 栈特定评审检查

- **DPI 缩放**：100% / 150% / 200% 各验证一次。图标或文字模糊是红线。
  - WPF：`PerMonitorV2` DPI 感知；用矢量图标。
  - Qt：`AA_EnableHighDpiScaling` + 矢量资源。
  - Electron/Tauri：CSS px 与 `devicePixelRatio` 处理正确。
- **系统高对比模式**：尊重 `HighContrast`（WPF）/ `QStyleHints`（Qt）/ `prefers-contrast`（Web）。
- **键盘**：完整 Tab 路径；焦点环可见；菜单可用键盘操作。
- **系统主题**：亮/暗跟随系统；无硬编码浅色假设。
- **窗口缩放**：布局在最小到最大窗口尺寸间不崩坏。
- **菜单与快捷键**：平台惯例（macOS 菜单栏 / Windows 菜单）。
- **屏幕阅读器**：`AutomationProperties`（WPF）/ `QAccessible`（Qt）/ ARIA（Web）。
- **线程安全**：UI 更新在主线程（WPF Dispatcher / Qt 主线程 / Web 主线程）。

## 栈特定反模式

- 绝对像素定位（不随 DPI/窗口缩放）。
- 发布框架默认外观（WPF 默认控件、Qt 默认 Fusion、Electron 默认裸 HTML 无设计）。
- 在非主线程更新 UI。
- 硬编码颜色（不引用资源/令牌）。
- 忽略系统主题（强制浅色或暗色）。
- 用位图图标在缩放后模糊。
- Electron/Tauri 忽略宿主窗口约束（如焦点环被 CSS 移除、系统菜单未接）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| 非 DPI 感知 | 高 DPI 下模糊 | PerMonitorV2 / 矢量资源 |
| 硬编码像素布局 | 窗口缩放崩坏 | 布局容器 + 相对单位 |
| 忽略系统高对比 | 可访问性红线 | 尊重系统覆盖 |
| 位图图标 | 缩放模糊 | 矢量图标 |
| 非主线程更新 UI | 崩溃/竞态 | 主线程调度 |
| 默认外观未覆写 | 等于没做设计 | 三层令牌覆写 |
| Electron 移除焦点环 | a11y 失败 | 保留或自定义可见焦点 |
