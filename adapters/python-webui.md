# Adapter: Python Web-UI（Gradio / Streamlit / NiceGUI / Reflex）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- `primitives.py` / `semantic.py` / `components.py`

widget 只从 `components.py` 导入，不得直接从 `primitives.py` 导入。

## design-spec 映射

| design-spec 字段 | Gradio | Streamlit | NiceGUI |
|---|---|---|---|
| color tokens | `.theme` + `gradio.themes` 覆写；**必须杀掉默认紫色** | `.streamlit/config.toml` 的 `[theme]` | `ui.colors()` + QSS 风格 `add_head_html` |
| type scale | theme 的 `font` + `font_mono` | theme 的 `font` | `ui.label` 的 Tailwind 类 |
| spacing | `Blocks` 容器 padding | `st.columns` 间距 | `ui.row/column` 的 `classes` |
| motion | 受限；`gr.update` 过渡要克制 | 受限 | 完整 CSS |
| radius | theme `radius` | theme `baseRadius` | Tailwind `rounded-*` |

**本栈硬规则**：框架默认主题（Gradio 紫、Streamlit 蓝）**计为一次 AI-slop 命中**。发布前必须覆写。

## 三方向门——如何呈现真实方向

- 每个方向 = 一个独立的 `demo_a.py` / `demo_b.py` / `demo_c.py`，均可单独运行，各 ≤100 行，各自覆写主题。
- 交付 `run_directions.sh`，在不同端口同时启动三者。
- Streamlit 可用 `streamlit run demo_a.py --server.port 8501` 等。
- 若无法执行，按 `../references/static-deliverable.md` 降级。

## 栈特定评审检查

- **首屏延迟**：重组件（>200ms）必须显示骨架。
- **状态存活**：交互后 rerun 不得重置用户输入。
- **移动布局**：Streamlit/Gradio 会折叠列；在 375px 验证。
- **主题一致性**：无残留默认色（尤其 Gradio 的紫、Streamlit 的蓝）。
- **组件数控制**：单页组件数超过信息架构需要时，拆页而非堆叠。

## 栈特定反模式

- 发布框架默认主题不做任何修改。
- 移动端堆叠 6 个 `st.columns` 而无折叠逻辑。
- 用 `st.markdown` 注入未走令牌的 CSS。
- 在 Gradio 里用 `gr.HTML` 绕过主题系统。
- NiceGUI 里直接写内联 `style=` 而不引用令牌。
- 用 emoji 作为 Gradio 组件标签的图标系统（slop 命中）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| Gradio 默认紫色主题 | slop #1 命中 | `gr.Theme` 覆写 |
| Streamlit rerun 全量重算 | 卡顿、状态丢失 | `st.session_state` + 缓存 |
| 列数在窄屏不折叠 | 横向滚动 | 响应式列数或 `use_container_width` |
| `st.markdown` 注入 HTML | 破坏主题一致性 | 走 theme 配置 |
| NiceGUI 内联样式 | 令牌被绕过 | `ui.colors()` + Tailwind 类 |
| 大组件无骨架 | 首屏白屏 | 占位 + 异步加载 |
