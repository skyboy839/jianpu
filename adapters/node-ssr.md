# Adapter: Node SSR（Next / Nuxt / Express-EJS / Pug / Handlebars）

## 环境能力标签（必填）

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载后立即识别，据此调整三方向门交付形态（见 `../references/static-deliverable.md`）。

## 三层结构义务（引用）

按 `../references/token-layering.md` 的"adapter 三层结构义务"执行。本栈三层文件：

- `tokens/primitive.ts` / `tokens.semantic.ts` / `theme/components.ts`（服务端渲染引用 CSS 变量）

模板只从 `theme/components.ts` 或其编译产物（CSS 变量 / 类名）引用，不得直接写 primitive 值。

## design-spec 映射

| design-spec 字段 | Next / Nuxt | Express + EJS/Pug/Handlebars |
|---|---|---|
| color tokens | CSS 自定义属性 + `tokens.css`（由 design-tokens 生成） | 同左；模板引用 CSS 变量 |
| type scale | Tailwind `text-*` 或 `theme.fontSize` | 模板类名或 CSS 变量 |
| spacing scale | Tailwind `space-*` / `gap-*` | 同左 |
| radius | Tailwind `rounded-*` | 同左 |
| motion | CSS 变量 + `prefers-reduced-motion` | 同左 |

**关键约束**：SSR 场景下，**首屏布局方向必须在数据接入前确定**。服务端渲染出来的第一帧就是用户看到的布局——它不能等客户端 JS 再调整。

## 三方向门——如何呈现真实方向

- Next / Nuxt：每个方向 = 一个真实路由（`/preview/a` 等）或 Storybook stories；用 Playwright 截图。
- Express + 模板：每个方向 = 一个独立的 `.ejs` / `.pug` 布局文件
  + 一条渲染路由；用 `curl | 截图服务` 或本地渲染后截图。
- **必须用真实数据接入后的首屏呈现方向**——不要用空数据骨架演示。
- static 环境按 `../references/static-deliverable.md` 降级。

## 栈特定评审检查

- **首屏即布局**：SSR 首帧必须是最终布局；无"服务端给骨架、客户端重排"的布局偏移。
- **hydration 一致性**：Next/Nuxt 的 SSR 与客户端首帧必须一致，否则闪烁（hydration mismatch 是红线）。
- **无 JS 可用**：核心内容与导航在 JS 关闭时仍可用。
- **表单**：服务端渲染的表单在 JS 加载前即可提交（渐进增强）。
- **响应式**：375 / 768 / 1440 三档评审。
- **暗色模式**：服务端需按用户偏好或媒体查询输出正确主题，避免"闪白"（FOUC）。
- **SEO 语义**：标题层级、`lang` 属性、结构化数据正确。

## 栈特定反模式

- 服务端渲染空壳、客户端 JS 才填内容（等于没做 SSR）。
- 布局依赖客户端测量（`window.innerWidth`）导致首帧错位。
- 主题在客户端才决定 → 暗色用户看到闪白。
- 模板里硬编码 hex / 像素值（绕过令牌）。
- 用 CSS-in-JS 在服务端注入未令牌化的样式。
- 手改生成的 `tokens.css`（应改 `design-tokens.json` 再生成）。

## 常见栈陷阱

| 陷阱 | 后果 | 规避 |
|---|---|---|
| SSR 与客户端首帧不一致 | hydration 报错 + 闪烁 | 首帧用同一数据源 |
| 主题在客户端才定 | 暗色用户闪白 | 服务端读取偏好/媒体查询 |
| 布局用客户端测量 | 首帧错位 + CLS | 用 CSS 布局，不靠 JS 测量 |
| 模板硬编码色值 | 令牌被绕过 | 统一引用 CSS 变量 |
| 表单依赖 JS | 无 JS 时不可用 | 渐进增强，原生 form 提交 |
| 生成文件被手改 | 下次生成被覆盖 | 只改源 JSON |
