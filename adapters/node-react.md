# Adapter: Node 前端（React / Vue / Svelte / Angular）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- `tokens.primitive.ts` / `tokens.semantic.ts` / `theme.components.ts`

组件只从 `theme.components.ts` 导入，不得直接从 `tokens.primitive.ts` 导入。Vue 同构（`tokens/primitive.ts` / `semantic.ts` / `theme/components.ts`）。

## design-spec 映射

| design-spec 字段 | React | Vue | Svelte |
|---|---|---|---|
| color tokens | CSS 自定义属性 + `theme.ts`（或 Tailwind `tailwind.config`） | CSS 自定义属性 + `v-bind` / `theme.ts` | CSS 自定义属性 + `$lib/tokens` |
| type scale | Tailwind `text-*` 或 `theme.fontSize` | 同左 | 同左 |
| spacing scale | Tailwind `space-*` / `gap-*` | 同左 | 同左 |
| radius | Tailwind `rounded-*` | 同左 | 同左 |
| motion | `framer-motion` 预设（集中定义一次） | `@vueuse/motion` | 原生 `transition:` + easing token |

**规则**：令牌住在 `design-tokens.json`；一个生成器（style-dictionary 或 20 行脚本）产出 `tokens.css` + `theme.ts`。**不要手改两处**。

## 三方向门——如何呈现真实方向

- 每个方向 = 一个真实路由（`/preview/a` / `/preview/b` / `/preview/c`），或三个 Storybook stories 在 1440×900 渲染。
- 不用文字描述方向。用户点击切换。
- capture 环境用 Playwright/Puppeteer 截三张 PNG。
- static 环境按 `../references/static-deliverable.md` 降级为 ASCII + 令牌表。

## 栈特定评审检查

- **响应式**：375 / 768 / 1440 三档都必须评审。
- **暗色模式**：每个 token 有成对 light/dark 值；无孤儿 hex。
- **可访问性**：焦点顺序、正文对比 ≥4.5:1、大字与 UI 组件 ≥3:1。
- **控制台干净**：零 React key 警告、零 hydration 不匹配。
- **布局偏移**：首屏 CLS ≤ 0.1。
- **键盘**：Tab 顺序符合视觉顺序；模态有焦点陷阱且可 Esc 退出。
- **语义 HTML**：用 `<button>` 不用可点击 `<div>`；标题层级连续。

## 栈特定反模式

- 发布组件库默认主题不做覆写（等于没做设计）。
- 任何可令牌化的东西用内联 `style={{}}`。
- emoji 作为图标系统而无 `aria-label` 兜底。
- 全站用 `system-ui` 而题材需要展示字体（slop #3 命中）。
- 用 `div` + `onClick` 冒充按钮（键盘不可达）。
- 同屏两套色板（一个组件库一套、自定义一套）。
- 状态靠颜色单独承载（无文本/图标双编码）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| 手改 `tokens.css` 与 `theme.ts` | 两处分叉 | 生成器单一来源 |
| `style={{}}` 混入令牌 | 主题不可预测 | 一律走 token/类 |
| 暗色模式简单反转浅色 | 对比度失效 | 独立检查暗色对比度 |
| 列表 key 用 index | 重排错乱 | 用稳定 id |
| 响应式断点硬编码 | 维护困难 | 断点入令牌 |
| 组件库默认色残留 | slop 命中 | 显式覆写并 grep 验证 |
| emoji 图标无 alt | a11y 失败 | 配 `aria-label` 或换 SVG |
