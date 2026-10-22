# Adapter: Python TUI（Textual / Rich / Prompt-toolkit）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- `tokens/primitive.tcss` / `semantic.tcss` / `components.tcss`

Textual 组件只从 `components.tcss` 导入样式，不得直接引用 `primitive.tcss`。

## design-spec 映射

| design-spec 字段 | Textual（TCSS） | Rich | Prompt-toolkit |
|---|---|---|---|
| color tokens | `$bg` / `$text` / `$accent` 变量 | `Style(color=...)` | `Style.from_dict({...})` |
| type scale | TCSS `text-style`（终端字号不可控，靠间距与字重建层级） | 同左 | 同左 |
| spacing scale | `padding` / `margin` 以格为单位 | `Padding(...)` | 布局容器间距 |
| radius | 不支持（终端无圆角）；用边框字符模拟 | 同左 | 同左 |
| motion | Textual 动画（`animate()`）；Rich/prompt-toolkit 受限 | 受限 | 受限 |

**终端的物理约束**：没有字号、没有圆角、没有真彩色保证。design-spec 的 `radius` 与 `typography.scale` 在 TUI 中**降级为字符间距与边框策略**——在 `design-spec.md.notes` 中记录降级映射。

## 色彩能力分级

TUI 必须声明色彩能力，不同能力下色板不同：

| 能力 | 色数 | 色板策略 |
|---|---|---|
| `mono` | 单色 | 仅靠字重、间距、边框字符建立层级 |
| `ansi-16` | 16 色 | 用语义色映射到 ANSI 基本色，不用真彩 hex |
| `ansi-256` | 256 色 | 从 primitive 层挑选最接近的 256 色索引 |
| `truecolor` | 1600 万色 | 完整使用 design-spec 色板 |

在 `design-spec.md.notes` 记录 `tui_color_capability: <级别>`。**降级时不得靠颜色承载信息**——这在 mono 下是硬要求。

## 三方向门——如何呈现真实方向

- 每个方向 = 一个可运行的 `textual run demo_a.py`，各 ≤80 行，一屏，真实令牌。
- 产物形态：`textual run --screenshot` 的 PNG（capture-capable），或 `ANSI` 转储（capture-disabled 时用 `script -q out.txt -c "textual run demo_a.py"` 抓帧）。
- 若连执行都不能，按 `../references/static-deliverable.md` 降级为 ASCII wireframe
  + 令牌表 + 截图占位。
- static 下，`information-architecture` 与 `minimalism` 可真分化；`kinetic-poetics` 的动效不可表达 → 按矩阵合并标注"渲染后验证"。

## 栈特定评审检查

- **色彩兼容**：在 mono / 16 色 / 256 色 / truecolor 终端各验证一次。truecolor 下的设计不得在 16 色下完全失效。
- **信息不靠色**：状态（成功/警告/危险）必须有文本或符号双编码。
- **键盘全覆盖**：每个操作有键位；快捷键在帮助中可见。
- **焦点可见**：焦点框在不同终端主题下可见（有些终端会反转焦点色）。
- **窄终端**：在 80 列下验证；更窄时优雅降级（不横向溢出）。
- **CJK 宽度**：CJK 字符占双宽，布局按显示宽度而非字符数计算。
- **重绘性能**：大表格滚动不卡；避免每次按键全量重绘。

## 栈特定反模式

- 只在 truecolor 终端测试，16 色下全部糊成一色。
- 用颜色作为唯一状态信号（mono 下信息丢失）。
- 80 列以下横向溢出（TUI 的"横向滚动条"等价红线）。
- 用 emoji 作为主要图标系统（终端字体支持不一 + slop 命中）。
- 忽略 CJK 双宽导致对齐错乱。
- 每次按键触发全量重绘（大表格下卡顿）。
- 把 Web 的圆角/阴影审美硬套到 TUI（终端不支持，强行模拟会很丑）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| 硬编码 truecolor hex | 低色终端失效 | 按色彩能力分级映射 |
| 按字符数算布局宽度 | CJK 错位 | 按显示宽度计算 |
| 焦点色被终端反转后不可见 | 键盘用户迷失 | 焦点用边框 + 高亮双编码 |
| 状态仅靠前景色 | 色盲/mono 用户丢失 | 文本 + 符号双编码 |
| 全量重绘 | 滚动卡顿 | 只重绘变化区域 |
| 依赖 emoji 图标 | 字体缺失显示方框 | 用 ASCII 符号或文本 |
