---
name: mira-research-protocol
description: Mira 投研协议 — 唤醒词路由 + 投研质量契约（thesis_state / must_refresh_if / claim taxonomy / fact-vs-judgment 分离）。触发词：「Mira, 研究 X」「Mira, 看一下 X」「Mira, 更新 X」「Mira, 监控 X」「按 Mira 方式看 X」「hi Mira」「投研协议」「研究 A 股通信 ETF」「008327 怎么投」「CPO/光模块/存储/黄金产业研究」。适用：单只股票/ETF/产业概念/财报事件/持仓复盘 — 任何需要「证据 + 判断 + 刷新条件」的严肃投研场景。
---

# Mira 投研协议

> **Mira 是什么** — 来自 [byteseek/Mira](https://github.com/byteseek/Mira)（193⭐ MIT）的**投研协议标准**：把每个研究主题当成**状态化对象**（不是文档），强制 `fact / inference / judgment` 分离，强制 `thesis_state` + `stale_after` + `must_refresh_if`，强制 18 项必填字段。
>
> **Mira 不是 AI 人物，不是交易机器人** — 它是一套**让 AI 帮你做严肃投研的纪律**。
>
> 本地参考库：`~/mira/`（含 MIRA.md / OPERATING_CONTRACT.md / monitoring-loop.md / research-loop.md / aapl-2026-04/）

---

## 🎯 唤醒词 / 路由

| 用户说 | 路由 | 深度 |
|---|---|---|
| `hi Mira` / `你好 Mira` / `Mira mode` / `Mira help` | 返回 START_HERE 卡片 | — |
| `Mira, 看一下 X` / `Mira quick_map X` | `quick_map`（轻判断 + source gap + refresh trigger） | 浅 |
| `Mira, 研究 X` | `research-loop` 走 12 步首版研究 | standard |
| `Mira, 更新 X` / `Mira, 监控 X` | `monitoring-loop` 8 状态机增量更新 | quick_map |
| `Mira, 财报 X` | 走 `earnings-report-analysis` skill（事件差分） | standard |
| `Mira, 复盘 X` / `持仓 X 怎么看` | `position-review-loop` | standard |
| `按 Mira 方式看 X` / `用 Mira 研究 X` | 触发 Mira mode（强制走研究协议） | standard |
| 产业概念（CPO / HBM / 光模块 / 黄金 / 存储 / 液冷 / 先进封装） | **`industry-concept-analysis` 分支** —— 8 件套产物 | deep_dive |
| ETF（新上市 / 资金流异动） | `etf-listing-analysis` / `etf-listing-discovery` | standard |

**如果用户没给具体任务但用唤醒词** → 返回 START_HERE 卡片（不要硬塞研究流）。

---

## 📋 强制契约（每份输出必走）

**6 道必走门**（按顺序）：

```
1. intent intake    — 拆主意图/次意图，声明假设，输出 routing card
2. analysis-routing — 确认 task_mode / research_object / market_scope / time_boundary
3. depth budget     — quick_map / standard / deep_dive
4. data gates       — 信息价值 / live-data / ingestion / quant
5. thesis state     — active / watch / stale / retired（7 种状态机）
6. output discipline — 18 项必填字段（见下）
```

**18 项必填字段**（`quick_map` 可内部隐式，**`standard`/`deep_dive` 必须显式或明确 waive**）：

```
task_mode · research_object · market_scope · time_boundary
output_language · depth_mode · primary_skill_or_loop · routing_basis
followup_prompt_mode · private_state_action
live_data_gate / quote_time / publish_time / live_freshness_status
ingestion_route / ingestion_artifacts
quant_dependency / calculation_gate
fact vs inference vs judgment 分离
judgment_confidence · confidence_basis · reversal_condition · base_rate_anchor
information_value · knowability_status（允许 irreducible_uncertainty 终态）
stale_after · must_refresh_if
```

---

## 🔁 Monitoring Loop（8 状态机）— ETF 监控直接套

每次做"更新"类任务（如 ETF cron 监控），按这个流程走：

```
scan-updates            → 扫增量（不重写整份研究）
  ↓
filter-noise           → 降噪：按 credibility / content_type / research_role 分层
  ↓
classify-incremental-claims → 把新信息拆成 claim：
                              标注 claim_type（事实/公司口径/承诺/指引/预测/假设/观点/弱信号/市场定价）
                              + source_speaker + verification_status
  ↓
write-monitor-log      → 写入当期监控记录
  ↓
assess-impact          → 新信息改变 thesis？改变框架？改变 overlay？
  ↓
update-expectation-map → 映射到预期变量：
                          revenue / margin / cash flow / capex / balance_sheet_risk
                          / valuation_multiple / risk_premium / positioning / catalyst_timing
  ↓
thesis-state-change    → 状态机转换（必须指向 evidence log）：
                          active ↔ watch ↔ stale
                          ↔ upgrade_watch ↔ downgrade_watch ↔ retired
  ↓
escalate-or-close      → 小更新关 / 核心前提变化升级回 research-loop
```

**9 条升级规则**（任一触发 → 升级回 research-loop）：

1. 财报/指引/重大公告改变核心判断
2. 原 thesis 关键证据被削弱
3. 原 thesis 关键承诺没兑现 / 公司口径与已验证事实冲突
4. 原本是 `assumption / forecast / company_claim` 的关键输入被新证据证实/证伪
5. 重大事件改变公司/行业/估值叙事
6. 长期跟踪指标连续恶化
7. 标的的主导定价变量发生变化（原框架可能失效）
8. 原 overlay 的关键传导链被证伪/失去增量价值
9. 用户要求重做完整研究

---

## 🏭 Industry-Concept-Analysis（8 件套）

**当研究对象是产业概念**（不是单只票）时，**必须先输出 8 件套**：

```
1. one_page_industry_map     — 一页版：一句话定义/当前判断/紧缺环节/利润池/股票代理/核心公式/关键争论/跟踪指标/证伪条件
2. concept_boundary          — 它是什么/解决什么/和相邻概念的区别
3. value_chain_map           — 上游输入/核心工艺/制造集成/下游客户/终端需求
4. demand_map                — 谁来买/为什么买/什么时候买
5. supply_map                — 谁来供/产能/良率/认证
6. pricing_mechanics         — 谁能提价/价格由什么决定/合同 vs spot
7. volume_mechanics          — 谁能放量/约束（认证/产能/良率/设备/库存/需求）
8. tightness_and_profit_pool_ranking  — 哪些环节紧供需/高溢价/只是传导
```

**完整产物**（按 `templates/industry-analysis-package/`）：
- `industry-map.md`
- `company-map.csv`（全球龙头/区域龙头/纯暴露/多元化/私有关键/上市代理）
- `evidence-log.csv`

**对 A 股 ETF 标的池**（008327 通信 ETF / 022502 黄金 ETF）的研究**必须走产业概念分支**，不直接套单票 framework。

---

## 🔬 Research Loop（12 步首版研究）

```
define                   → 明确主题/问题/时间边界
route-analysis           → 走 analysis-routing
depth-budget             → quick_map / standard / deep_dive
industry-concept         → [可选] 产业概念走 8 件套
route-framework          → [仅单票] 选 micro-small / mid-cap / large-mega
select-overlays          → supply-chain / macro / commodity / strategic-catalyst / valuation-expectation
collect                  → 按框架+overlay 收来源
classify-claims          → 拆 claim：claim_type/source_speaker/verification_status
quant-check              → 数值依赖 → data-analysis-quality-gate
scan                     → 公司/财务/技术/事件 4 视角初判
gap-check                → 按 research-readiness-gate 预评 readiness_level
refine                   → 补足关键来源
package                  → 输出 research package
write-thesis-ledger      → 写 thesis-ledger.md + expectation-map.csv + decision-log.csv
write-memory             → 沉淀到 memory/（私人放 private/，产品放 memory/）
```

**3 条停止规则**：
- `max_iterations = 3`（防无限钻）
- 关键问题只依赖 L4/L6 来源 → 降级结论
- 关键来源缺失 → 允许输出"证据不足"

---

## 💡 Investment Memo 模板（AAPL case 范本）

每份严肃研究产物的**投资备忘录**包含 9 个核心字段：

```markdown
# {标的} Investment Memo

## Decision Header
- research_action: no_action / monitor / add / trim / rebalance
- conviction: high / medium / low
- horizon: 3-6m / 6-12m / 12-24m / 24m+
- what_is_priced_in: {市场已反映什么}
- invalidation_level_or_condition: {什么事件/价位证伪 thesis}
- implied_risk_reward: source_gap / 具体数字
- next_catalyst_date: {下个催化剂}

## Core Conclusion    （一句话核心判断）

## Bull Case          （3-4 条多头逻辑）

## Bear Case          （3-4 条空头逻辑）

## Key Debate         （核心分歧 = 不是"是不是好公司"，是"估值是否已 price in"）

## Valuation And Expectation Quant
- current_valuation_anchor
- what_is_priced_in
- base_case / bull_case / bear_case
- valuation_anchor_quality（fact / L2 / L5 / source_gap）

## Actionability Bridge
- research_action / setup_type / invalidation / implied_risk_reward
- required_followup

## Major Risks        （3-5 条）

## Tracking Metrics   （5 条）
- 跟踪指标 1
- 跟踪指标 2
...

## Must Refresh If    （5 条触发条件）
- 触发条件 1
- 触发条件 2
...

## Progressive Follow-Up
- follow_up 1: rung / route_binding / object_anchor / decision_impact
- follow_up 2
- follow_up 3
```

---

## 🎯 Thesis State 状态机

```
                    [user trigger]
                          ↓
                    ┌──────────┐
                    │  draft   │  ← 初始建立
                    └────┬─────┘
                         ↓
                    ┌──────────┐
         ┌─────────→│  active  │←──────────┐
         │          └────┬─────┘           │
         │               ↓                 │
    ┌────────┐  ┌──────────────┐    ┌──────────┐
    │ stale  │←→│  watch       │→   │ upgrade_watch  │ (催化临近)
    └────────┘  │ (观察区)     │    └──────────┘
         ↑      └──────┬───────┘
         │             ↓
         │      ┌────────────────┐
         │      │ downgrade_watch │  (证伪累积)
         │      └────────┬───────┘
         │               ↓
         │      ┌──────────┐
         └──────│ retired  │  ← 退出研究
                └──────────┘
```

**状态变化必须指向 evidence log** —— **不能无证据改状态**。

---

## 📦 Quick Map 输出模板（轻量级）

如果用户说"Mira, 看一下 X"（轻量级），输出 8 字段卡片：

```markdown
## Mira Quick Map: {X}

| 字段 | 值 |
|---|---|
| research_object | {X} |
| market_scope | A 股 / 美股 / 港股 / 行业 / 产业 |
| time_boundary | YYYY-MM-DD |
| 核心判断 | {一句话} |
| judgment_confidence | high / medium / low |
| reversal_condition | {什么事件证伪} |
| stale_after | YYYY-MM-DD |
| must_refresh_if | 条件 1 / 条件 2 / ... |

### 关键证据
- 证据 1（来源 + 时间）
- 证据 2
- 证据 3

### Source Gap（拿不到什么）
- 数据 1
- 数据 2

### Next Action
- monitor / add-research / trigger-loop / no-action
```

---

## 🛡️ 强约束（不要违反）

1. **不写交易指令** — Mira 输出 research_action（monitor/add/trim/no_action），**不直接说"买入/卖出"**
2. **事实 ≠ 推断 ≠ 判断** — 任何结论必须能回溯到 evidence log
3. **每个判断带 confidence + reversal_condition** — 不带的话只能算 hypothesis
4. **stale_after 必须写** — 过期不更新就降级
5. **must_refresh_if 必须写 5 条** — 触发就升级回 research-loop
6. **private state 不进 tracked 仓库** — 用户持仓/观点放 `private/`，产品协议放 `~/mira/`
7. **industry concept 不套单票 framework** — CPO/HBM/光模块/黄金/存储/液冷 一律先 8 件套
8. **max_iterations = 3** — 别无限钻
9. **承认 irreducible_uncertainty** — 拿不到就明说"irreducible_uncertainty"，不要硬编

---

## 📁 本地参考库（~/mira/）

| 文件 | 用途 |
|---|---|
| `MIRA.md` (11KB) | 身份契约 + 唤醒词 + 18 项必填字段 |
| `OPERATING_CONTRACT.md` (15KB) | 路由表 + lazy loading map + 4 道数据门 |
| `AGENT_QUICKSTART.md` (19KB) | 完整 quickstart + 输出位置 |
| `AGENTS.md` (5KB) | 路由规则（自动注入） |
| `START_HERE.md` (7KB) | 用户第一问/帮助时的卡片 |
| `loops/monitoring-loop.md` (4.6KB) | 8 状态机（ETF 监控直接套） |
| `loops/research-loop.md` (11.3KB) | 12 步首版研究 |
| `cases/aapl-2026-04/investment-memo.md` (6.5KB) | Memo 模板 |
| `architecture/thesis-system.md` (20KB+) | Thesis 状态化机制 |
| `architecture/data-acquisition-upgrade.md` | stdlib 数据 substrate（P1-P4 已实现） |

**详细模式或具体步骤** → `cat ~/mira/loops/monitoring-loop.md` 或对应文件。

---

## 🧪 怎么用这个 skill

**对 agent 说**（任何一条触发词都行）：

```
"Mira, 看一下 008327 通信 ETF 今天能不能买"
"Mira, 研究光模块产业（CPO/硅光/1.6T 路线）"
"Mira, 更新一下中际旭创这只票"
"按 Mira 方式做一份 008327 通信 ETF 的 industry-analysis-package"
"Mira, 008327 通信 ETF 的 must_refresh_if 是什么"
```

**agent 应该**：
1. 检测唤醒词 → 进入 Mira mode
2. 按路由表选 loop（research/monitoring/industry-concept）
3. 走 6 道必走门
4. 输出符合 18 项必填字段的研究产物
5. 强制 `thesis_state` + `stale_after` + `must_refresh_if`
6. 给出 Progressive Follow-Up（让用户决定下一步）

---

## 🔗 跟其他 skill 的关系

- **`etf-sentiment-monitor`**：B 任务会改它，嵌入 `thesis_state` 状态机
- **`sector-sentiment-akshare`**：监控数据源，evidence log 的一部分
- **`html-dashboard-github-pages`**：可视化 monitor 输出
- **`chrome-cdp-bypass-waf`**：拿雪球/财经数据的执行器
- **`aily-writer`**：写正式投研文档（Feishu doc）
- **`finance-news-pro`**：新闻 sentiment 分析

---

## ⚠️ 必读的实操 Pitfalls

构建 Mira 协议产物（特别是 industry-analysis-package）时**必踩的 7 个坑**：

- akshare `fund_portfolio_hold_em` 返回全季度数据 → 跨季度加总权重爆表
- Mira evidence-log 列数是 **20 列**（AAPL header 验证），不是 22 列
- patch 工具改 f-string block 含 emoji 时会折叠重复 `msg += f"""` 行
- WSL → GitHub `git clone` 必超时，必须用 API + tarball 或 raw.githubusercontent.com
- 18 项必填字段容易漏 `routing_basis` / `private_state_action` / `live_data_gate` / `quant_dependency` 4 项
- A 股 ETF 季报只披露 Top 15，另 50% 是"非披露小票"
- industry-concept-analysis "概念边界"要画清"它不是什么"+ 5 个相邻概念对比

**完整避坑指南 + 自检代码** → `references/mira-protocol-pitfalls.md`（7 个 pitfall + 解决方案）

**evidence-log.csv 标准 20 列 header + 6 行示例 claim** → `templates/evidence-log-csv-header.md`（直接复制可用）

**quick_map 轻量级输出模板** → `templates/quick-map-output.md`（用户说"Mira, 看一下 X"时直接套）
