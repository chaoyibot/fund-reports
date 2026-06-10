# AAPL-Style Investment Memo 模板

> 来自 `~/mira/cases/aapl-2026-04/investment-memo.md`（byteseek/Mira 公开案例）
> 适用：A 股个股 / ETF / 产业 / 港股 / 美股 — 任何"严肃投研产物"

---

## 模板（直接抄）

```markdown
# {标的} ({代码}) Investment Memo

- market: {US / A 股 / 港股}
- output_language: {zh-CN / en-US}
- research_question: {这次研究要回答的核心问题}
- research_cutoff_date: YYYY-MM-DD
- financial_data_through: {最近一期财报期}
- price_date: YYYY-MM-DD
- thesis_horizon: {3-6m / 6-12m / 12-24m / 24m+}
- stale_after: {下次财报 or 30d/60d/90d}

## Decision Header

| field | value |
|---|---|
| research_action | `no_action` / `monitor` / `add` / `trim` / `rebalance` |
| conviction | high / medium / low — 按 evidence strength + 反方证据厚度 |
| horizon | 12-24 months, stale before live use |
| what_is_priced_in | {市场已经反映了什么} |
| invalidation_level_or_condition | {什么事件/价位证伪 thesis} |
| implied_risk_reward | source_gap 或具体数字 |
| next_catalyst_date | {下个催化剂日期} |

## Core Conclusion

{一句话核心判断 — 包含 标的/方向/置信度/horizon}

## Bull Case

- 逻辑 1（数据 + 来源）
- 逻辑 2（数据 + 来源）
- 逻辑 3（数据 + 来源）
- 逻辑 4（数据 + 来源）

## Bear Case

- 风险 1（数据 + 来源）
- 风险 2（数据 + 来源）
- 风险 3（数据 + 来源）
- 风险 4（数据 + 来源）

## Key Debate

{核心分歧 — 不是"是不是好公司"，而是"估值是否已 price in"}
{是好公司问题 vs 便宜股票问题}

## Valuation And Expectation Quant

| item | value |
|---|---|
| current_valuation_anchor | {PE / PS / EV/EBITDA / DCF / source_gap} |
| what_is_priced_in | {市场已认什么} |
| base_case | {基准情形假设 + 推论} |
| bull_case | {多头路径需要什么} |
| bear_case | {空头路径来自什么} |
| valuation_anchor_quality | fact / L2 / L5 / source_gap |

## Actionability Bridge

| field | value |
|---|---|
| research_action | no_action / monitor / add / trim / rebalance |
| setup_type | {quality compounder / variant-dislocation / catalyst-driven / mean-reversion} |
| invalidation | {具体证伪条件} |
| implied_risk_reward | source_gap 或数字 |
| required_followup | {必补的研究步骤} |

## Major Risks

- 风险 1
- 风险 2
- 风险 3
- 风险 4
- 风险 5

## Tracking Metrics

- 跟踪指标 1（监测什么 / 阈值）
- 跟踪指标 2
- 跟踪指标 3
- 跟踪指标 4
- 跟踪指标 5

## Must Refresh If

- 触发条件 1（事件 / 价位 / 数据点）
- 触发条件 2
- 触发条件 3
- 触发条件 4
- 触发条件 5

## Progressive Follow-Up

1. follow_up 1
   - rung: Rung A / B / C
   - route_binding: {哪个 loop / skill / 模板}
   - object_anchor: {具体变量}
   - decision_impact: {影响什么决策}

2. follow_up 2
   - rung / route_binding / object_anchor / decision_impact

3. follow_up 3
   - rung / route_binding / object_anchor / decision_impact
```

---

## 实际示例：AAPL（截取）

> 完整版：`~/mira/cases/aapl-2026-04/investment-memo.md`

```markdown
# Apple Inc. (AAPL) Investment Memo

- research_cutoff_date: 2026-04-14
- price_date: 2026-04-13
- thesis_horizon: 12-24 months
- stale_after: next quarterly results release or 2026-07-13

## Decision Header
| research_action | no_action |
| conviction | medium on business quality, low on new upside actionability |
| invalidation_level_or_condition | sustained break below 245 or earnings evidence of services/margin deterioration |
| implied_risk_reward | source_gap |

## Core Conclusion
Apple 仍符合高质量中长线核心资产的定义，但在 2026-04-14 这个时点，
更像"继续跟踪和持有的优质平台"而不是"明显错杀后的高弹性新机会"。

## Key Debate
核心分歧不在"Apple 是不是好公司"，而在"当前估值是否已经充分反映其
高质量、强现金流与产品周期改善"。换言之，这是优质公司问题，不一定是便宜股票问题。
```

---

## 强制约束（不要违反）

1. **每个 material judgment 必带 `judgment_confidence` + `reversal_condition`**
2. **fact / inference / judgment 必须分离**（不要混在一段话里）
3. **valuation_anchor_quality 不能瞎写**——不知道就写 `source_gap`
4. **must_refresh_if 至少 5 条**（少 1 条不让过）
5. **progressive follow-up 3 条**（带 rung / route_binding / object_anchor / decision_impact）
6. **不写"买入/卖出"**——只写 `research_action`（add/trim/monitor/no_action）
7. **stale_after 必填**——不填视为"无限期有效"，违反 Mira 协议
8. **承认 `irreducible_uncertainty`**——估值锚算不出来时不要硬编

---

## 跟 A 股 ETF 的对应关系

| AAPL case 字段 | A 股 ETF 对应 |
|---|---|
| `ticker: AAPL` | `fund_code: 008327`（东财通信 ETF） |
| `market: US` | `market: A 股 ETF` |
| `price_date` | `nav_date`（净值日期） |
| `stale_after: 2026-07-13` | `stale_after: 7d / 30d / 下次调仓` |
| `evidence_log` | `evidence_log.csv`（akshare 板块情绪 + 雪球 7×24 + 财联社早报 + 东方财富研报） |
| `invalidation: 245 美元` | `invalidation: 净值破前低 + 板块情绪转负` |
| `Tracking: 财报 / 关税` | `Tracking: 通信板块资金流 / 龙头股业绩 / 政策事件` |
| `Must Refresh If: 财报 / 折叠屏延期` | `Must Refresh If: 5G-A 政策 / CPO 路线 / 中际旭创业绩 / 通信 ETF 大额申赎` |

**008327 通信 ETF memo 的 5 个 must_refresh_if 例子**：
1. 中际旭创/新易盛/天孚通信 发布季报或业绩预告
2. 5G-A 政策/工信部招标出现重大变化
3. CPO/硅光路线出现关键节点（量产/延期/客户认证）
4. 通信 ETF 出现单日 > 5% 净申赎
5. 板块情绪分（akshare 23 只成分股加权）连续 3 日转负
