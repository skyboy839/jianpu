# 外观账本 / Appearance Ledger

跨项目持久化的 `appearance-ledger.json`，让本工作区下一个项目**刻意不同**。账本同时是完整包交付包的前置依赖。

## 字段结构

```json
{
  "version": 1,
  "entries": [
    {
      "id": "proj-2026-10-a",
      "date": "2026-10-22",
      "stack": "python-desktop",
      "philosophy": "minimalism",
      "movement": "minimal",
      "brand_version": "2.1.0",
      "brand_version_changed": false,
      "brand_locked": ["accent", "bg"],
      "signature": {
        "bg":      "oklch(0.98 0.005 250)",
        "accent":  "oklch(0.62 0.16 30)",
        "type_ui": "IBM Plex Sans",
        "layout":  "single-column, left-nav 200px",
        "notable": ["no cards", "mono-only metrics"]
      },
      "rejected_directions": [
        "kinetic-poetics: motion too costly for on-call context",
        "experimental: novelty competes with alarm signal"
      ],
      "primitive_bump": null,
      "semantic_bump": null,
      "component_bump": null
    }
  ]
}
```

## 字段豁免（品牌锁定）

`brand_locked` 的语义与锁定层级见 `brand-asset-protocol.md` 的「品牌锁定」小节——**本文件不重复定义锁定层级**。

本文件只定义 `brand_locked` 在账本中的使用行为：
- 差异检查**跳过** `brand_locked` 中的字段。
- 只在未锁定字段（如 `layout` / `type` / `notable`）上要求差异化。
- 品牌版本变更时按下方「品牌版本变更」处理。

## 品牌版本变更

若 brand-spec 因版本升级被重验：
- 旧条目保留历史，标记 `brand_version_changed: true`。
- 旧条目的 `brand_locked` 字段保留供审计，不用于新项目差异检查。
- 新项目按新 brand-spec 的 `brand_locked` 执行。

## 下一项目如何使用

1. 加载本栈最近 3 条 entries。
2. 差异检查：在三方向选择阶段，确保**至少两个方向**与最近项目的 `signature` 在 ≥2 个**未锁定**字段上不同。
3. 记录差异化理由到 `design-spec.md.notes`。

## 与三方向门的关系

- 三方向门的"选择启发式"（three-directions-gate.md 步骤 3）从账本 `rejected_directions` 反排除。
- 三方向门的硬约束（≥2 个探索其它 philosophy）优先于账本差异建议。若两者冲突，硬约束胜出。

## static 交付的账本

static 交付时 `signature` 字段可用 ASCII 布局描述 + 令牌摘要代替，确保下一个项目仍可对标差异。

## 版本化字段

- primitive token 更新 → 记 `primitive_bump`（如 `"1.2.0"`）。
- semantic token 更新 → `semantic_bump`。
- component API 更新 → `component_bump`。字段在 entry 中记录，供审计。分层与 bump 规则见 `token-layering.md` 的「版本化」小节。

## 豁免场景

纯缺陷修复的精简包**可不引用账本**（见 `handoff.md` 与 SKILL.md 纪律 7）。完整包必须引用账本条目 id。
