# Adapter: Python 桌面（PyQt / PySide / Tkinter / wxPython）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- `primitives.py` / `semantic.py` / `components.py`

widget 只从 `components.py` 导入，不得直接从 `primitives.py` 导入。

## design-spec 映射

| design-spec 字段 | PyQt / PySide | Tkinter | wxPython |
|---|---|---|---|
| color tokens | `QSS` 自定义属性 + `QPalette` | `ttk.Style` 主题 + 命名颜色 dict | `wx.Colour` 常量模块 |
| type scale | `QFont` 的 `pt` 字号 + `setStyleSheet` | `font=(family, size)` 元组 | `wx.Font` |
| spacing scale | `layout.setSpacing(n)` / `setContentsMargins` | `grid(padx, pady)` | `sizer` 的 border |
| radius | QSS `border-radius` | 非原生；用自定义 canvas 或跳过 | `wx.BORDER_NONE` + 自绘 |
| motion | `QPropertyAnimation` + 命名 easing | 受限；记录降级方案 | `wx.Timer` + 手动插值 |

**冻结为 `theme/` 三层模块**。所有 widget 从 `components.py` 导入。

## 三方向门——如何呈现真实方向

- 每个方向 = 一个**可运行最小窗口**（`python -m demo.direction_a`），≤80 行，一屏，真实令牌。
- 不用文字描述方向。三个窗口并排展示，用 `QWidget.grab().save(...)` 或 `pyautogui.screenshot` 抓图。
- headless 环境：提供 `make directions` 一次输出三张 PNG；若连执行都不能，按 `../references/static-deliverable.md` 降级为 ASCII。

## 栈特定评审检查

- **DPI 缩放**：在 100% / 150% / 200% 下验证。图标模糊是红线。
- **焦点环**：每个交互 widget 必须有可见焦点态（键盘走一遍）。
- **高对比模式**：检查 `QStyle` 是否尊重系统主题覆盖。
- **长字符串**：标签截断必须有省略号，不能无声截断。
- **线程安全**：信号处理器中的阻塞工作是功能性红线。
- **屏幕阅读器**：设置 `setAccessibleName` / `setAccessibleDescription`。

## 栈特定反模式

- 用绝对像素几何（`widget.setGeometry`）而布局管理器可解决时。
- 同一应用混用多个 UI 工具包。
- 业务逻辑写在 widget 子类里（应推到 controller / model）。
- 硬编码颜色到 QSS 而不引用 `semantic.py`。
- 忽略 `QApplication.setAttribute(Qt.AA_EnableHighDpiScaling)`。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| 默认 Fusion 风格直接发布 | 等于没做设计 | 用 QSS 或 `QPalette` 覆写 |
| 用 `setFixedSize` 固定窗口 | 破坏缩放与可访问性 | 布局管理器 + 最小尺寸 |
| 信号槽里跑长任务 | UI 冻结 | `QThread` / `QThreadPool` |
| QSS 与 QPalette 混用无优先级 | 主题不可预测 | 二选一为主，另一个只做局部覆盖 |
| 忽略 CJK 字体回退 | 中文显示为方块 | 字体栈显式包含 CJK |
