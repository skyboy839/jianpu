# 数据可视化跨栈规范 / Data Visualization

## 触发

任何栈中出现：图表（折线、柱状、饼图、散点、地图）、表格（带视觉编码）、实时数据流（仪表盘、监控面板）。

## 三条跨栈规则

### 1. 色板语义化

- 数据系列色不自由选择——按**语义**分配：
  - 类别色（categorical）：3–8 色，色相分离，不用于状态。
  - 序列色（sequential）：单色相明度渐变，表达强度。
  - 发散色（diverging）：两极 + 中性，表达偏差。
- 状态色（成功/警告/危险）**保留**，不与数据系列色混用。
- 色板在 `design-spec.md.colors.dataviz` 单独成节，light/dark 成对。

### 2. 图例与轴一致性

- 图例顺序 = 数据系列在视觉中的顺序（从上到下或从左到右）。
- 轴标签指向单位；数值轴刻度对齐。
- 同一数据集在同一产品内色板恒定——不因页面而变。

### 3. 图表 a11y

- 每个图表有文本等价物（数据表或描述）。
- 颜色不单独承载信息——加形状、纹理或标注。
- 交互图表可键盘操作（Tab 到数据点，或提供数据表）。
- 对比度：图元与背景 ≥3:1。

## 各栈落点

| 栈 | 实现要点 |
|---|---|
| React | ECharts / Recharts / D3 的 theme 对象引用 `design-spec.colors.dataviz` |
| Vue | 同上，Vue 版图表库（vue-echarts） |
| Python 桌面 | matplotlib 的 `rcParams` 或 PyQtGraph 的 `setPen` |
| Python Web-UI | Gradio `gr.Plot` 覆写 theme；Streamlit `st.plotly_chart` 传 custom theme |
| Textual | ASCII chart（`plotext` / Textual `Sparkline`）遵循色彩约束 |
| Mobile | Swift Charts / Compose Charts 主题覆写 |
| Flutter | `charts_flutter` / `fl_chart` 的 Color 配置引用 tokens |
| RN | `victory-native` / `react-native-chart-kit` 的主题配置 |
| 原生桌面 | Qt Charts / WPF LiveCharts 主题对象 |

## 硬规则

- **不得**用色相区分 >8 个类别（改用分组或交互筛选）。
- **不得**用红/绿单独区分（色盲）——加形状或标签。
- **不得**在仪表盘上同时存在两套不一致的色板。
- **必须**为大数字配单位与千分位——信息建筑派的硬约束。

## 与设计哲学的关系

- 信息建筑派：默认使用本规范。
- 极简主义派：可降至 ≤3 系列色，但硬规则不变。
- 运动诗学派：实时动效必须保持可关闭（a11y-baseline）。

## 数据密度下限（信息建筑派硬约束）

- 表格视图每屏 ≥8 行或 ≥3 个指标。
- 卡片视图每卡 ≥1 关键指标 + 1 上下文指标。
- 无数据时不得用 lorem ipsum——用真实空状态文案。
