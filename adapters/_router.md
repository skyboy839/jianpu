# Adapter Router / 路由规则

## 用途

本文件是栈路由的**细化规则**。SKILL.md 的路由表是**唯一权威**；本文件只补充"当信号模糊时如何判定"，不新增栈分类、不重复路由表。

## 判定优先级

按以下顺序检查工作区信号，命中即停：

1. **用户自己的原话**——"我的 PyQt 窗口""这个 React 页面" "我的 Streamlit 应用"。用户的命名优先于文件扫描。
2. **当前正在编辑的文件扩展名**——`.py` 还是 `.tsx` 还是 `.dart`。
3. **依赖清单**——`requirements.txt` / `pyproject.toml` / `package.json` / `pubspec.yaml` / `*.csproj` / `Package.swift`。
4. **入口文件**——`main.py` / `app.py` / `src/App.tsx` / `lib/main.dart`。

## 模糊场景的判定

### 场景 1：工作区同时有 Python 和 Node

例如：Python 后端 + React 前端。

**判定**：看用户描述的**渲染层**，不看后端。若用户说"优化界面"，大概率指 React 前端 → `node-react.md`。若用户说"我的桌面工具"，指 Python → `python-desktop.md`。

若仍不清晰，问一个封闭问题："你指的是哪个界面——桌面窗口还是网页？"

### 场景 2：PyQt 应用内嵌 QWebEngineView

**判定**：仍是 `python-desktop.md`。内嵌的 HTML 从属于宿主窗口，不切换 adapter。设计判断以**宿主窗口**的约束为准（DPI、焦点、系统高对比），网页内部的 CSS 是次要媒介。

### 场景 3：Electron / Tauri

**判定**：`native-desktop.md`。它们是桌面应用（Web 前端渲染），不是"Node 前端"也不是"声明式 UI 栈"。路由表已明确。

### 场景 4：Flutter / RN

**判定**：`mobile.md`。它们是声明式栈——加载 `mobile.md` 后，按 SKILL.md 的「声明式栈注意」**额外加载** `../references/declarative-mapping.md`。

### 场景 5：Rust + Tauri

**判定**：`native-desktop.md`（Web 前端渲染，非声明式 UI 栈）。路由表已明确。

### 场景 6：Rust + egui / iced

**判定**：`generic.md`（声明式注意触发）。加载 generic 后，按 generic 的「已知声明式栈清单」自动加载 `../references/declarative-mapping.md`。

### 场景 7：未知栈

**判定**：`generic.md`，然后问用户一个封闭问题：

> "这是桌面窗口、网页、终端界面，还是移动应用？"

根据回答重新路由。不要在未知栈上猜测后直接产出——猜错的成本高于多问一句。

## 绝不混用 adapter

一次任务只加载**一个** adapter。理由：adapter 定义的是**渲染层的约束**（DPI、响应式、终端色彩），混用会导致约束冲突。

**唯一例外**：多端一致性任务——此时显式加载多个 adapter，每个 adapter 治理各自的渲染层，由 `../references/token-layering.md` 的 primitive/semantic 层保持跨栈一致。

## 环境能力标签

每个 adapter 顶部标注环境能力（见 `../references/static-deliverable.md`）：

- `runnable` / `static-only`
- `capture-capable` / `capture-disabled`

加载 adapter 后**立即识别**当前环境的标签，据此调整三方向门的交付形态（见 `../references/three-directions-gate.md` 总则第 4 条）。标签不降低方向数量或分化度。

## 与 SKILL.md 路由表的关系

- **SKILL.md 路由表**：定义"什么信号 → 什么 adapter"，是分类权威。
- **本文件**：定义"信号模糊时怎么判"和"判定优先级"，是判定规则。

两者不重复。若本文件与 SKILL.md 路由表冲突，以 SKILL.md 为准。

## 反模式

- 在未知栈上猜 adapter 而不问。
- 混用多个 adapter 而不显式声明是多端任务。
- 忽略用户原话，只靠文件扫描。
- 把内嵌 WebView 的宿主窗口路由到 node-react。
