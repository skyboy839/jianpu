# 品牌资产协议 / Brand Asset Protocol

## 目标

把品牌资产（色、字、logo）从外部来源提取为**可信的固化产物** `brand-spec.md`，供后续项目直接引用。核心纪律是"绝不凭 Agent 自身记忆猜"。

## 五步流程

1. **问**：问用户是否有品牌手册 / 官网 / VI 文件。
2. **搜官方渠道**：官网、官方社媒、官方 PDF。
3. **下载资产**（三条兜底）：
   - SVG logo。
   - 官网 HTML + CSS。
   - 产品截图（最次兜底）。
4. **grep 提取色值**：
   - 从 CSS / SVG 中提取所有十六进制 / OKLCH / RGB。
   - 按**出现频率排序**。
   - 过滤纯黑 / 纯白 / 中性灰。
   - 剩余前 3–5 个作为候选品牌色。
5. **固化**：
   - 写入 `brand-spec.md`。
   - 头部声明 `brand_version: <string>`（来自品牌方或自定）。
   - 色值转为 OKLCH（hex 保留为注释）。
   - 同步写入 `tokens.primitive.*`。

## 信任分级（关键）

读到不等于信任——读是为了发现，信任是为了使用。

| 状态 | 信任级别 | 行动 |
|---|---|---|
| `brand-spec.md` 存在且 ≤30 天且无版本升级信号 | **信任** | 直接读，跳过五步 |
| `brand-spec.md` 存在但 >30 天或检测到版本升级信号 | **降级为不信任** | 走五步重验 |
| 仅 `tokens.css` 无 `brand-spec.md` | **不信任** | 走五步重提取 |
| 用户口述色值 | **低信任采纳** | 记录"用户口述来源"，下次可重验；**不视为已固化品牌色**，不进入 `brand_locked` |

**版本升级信号**：
- `brand/CHANGELOG.md` 中的版本号 > `brand-spec.md.brand_version`。
- 品牌方公开声明的品牌版本号变化。
- 官网主色明显变化（可人工核实）。

## 品牌锁定

`brand-spec.md` 中被品牌方**强制规定**的字段（通常是 accent 和 bg）需写入 `brand_locked`：

```yaml
# brand-spec.md
brand_version: "2.1.0"
brand_locked: ["accent", "bg"]
colors:
  accent: oklch(0.62 0.16 30)
  bg:     oklch(0.98 0.005 250)
```

**锁定层级**：`brand_locked` 锁定的是 **primitive 层的具体色值**（如 accent 的 OKLCH 值），不锁 semantic 层的映射。semantic 层可自由映射到已锁定的 primitive 或未锁定的 primitive——这保留了设计自由度，同时防止与品牌冲突。

本小节是 `brand_locked` 语义与锁定层级的**唯一权威定义**。`appearance-ledger-schema.md` 和 `token-layering.md` 只引用，不重复定义。

`brand_locked` 会传递到 `appearance-ledger.json` 的对应条目，让下一项目的账本差异检查**跳过**这些字段。

## brand-spec 版本变更时的账本同步

若 brand-spec 因版本升级被重验：
- 旧账本条目标记 `brand_version_changed: true`。
- 旧条目中 `brand_locked` 字段保留历史值供审计，但不再用于新项目的差异检查。
- 新项目按新 brand-spec 的 `brand_locked` 执行。

## 与纪律 1（从上下文生长）的关系

纪律 1 说"先读所有已有产物"。读到不等于信任——本协议定义**读后如何判断是否信任**。两者不矛盾：读是为了发现，信任是为了使用。

## 与纪律 3（绝不猜色）的关系

纪律 3 的"绝不凭 Agent 自身记忆猜"与本协议的关系：用户口述的色值可采纳，但标记为低信任、不进入 `brand_locked`——它来自用户的记忆，可能过时，需可重验。

## 反模式

- 从 Agent 自身记忆猜品牌色（如"我记得某品牌是红色"）。
- 用 `tokens.css` 代替 `brand-spec.md` 直接信任。
- 跳过频率排序，直接用截图里最显眼的色。
- 品牌更新后不重验，继续用旧 `brand-spec.md`。
- 写入 `brand-spec.md` 时不记录 `brand_version`（导致下次无法判断是否过时）。
- 把用户口述色值写入 `brand_locked`（口述是低信任，不固化）。
