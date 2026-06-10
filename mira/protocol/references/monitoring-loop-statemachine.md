# Monitoring Loop 8 状态机 — 投研协议

> 来自 `~/mira/loops/monitoring-loop.md`（byteseek/Mira MIT）
> 适用：ETF 监控 / 持仓复盘 / 行业盯盘 / 任何"持续跟踪"类研究任务

---

## 8 个状态机

```
┌─────────────────────────────────────────────────────────────┐
│  1. scan-updates                  扫增量，不重写整份研究    │
│        ↓                                                    │
│  2. filter-noise                  降噪：credibility 分层     │
│        ↓                                                    │
│  3. classify-incremental-claims   拆 claim：8 类 claim_type │
│        ↓                                                    │
│  4. write-monitor-log             写入当期监控记录          │
│        ↓                                                    │
│  5. assess-impact                 改变 thesis？框架？overlay？│
│        ↓                                                    │
│  6. update-expectation-map        映射 9 个预期变量          │
│        ↓                                                    │
│  7. thesis-state-change           7 种状态转换（必指 evidence）│
│        ↓                                                    │
│  8. escalate-or-close             小更新关 / 核心变化升级     │
└─────────────────────────────────────────────────────────────┘
```

---

## 状态 1：scan-updates

**目标**：扫增量信息，不重写整份研究。

**做法**：
- 读 6 类 monitor role 各自负责的数据源
- 按 `last_research_package` 的 `as_of_date` 之后的内容筛选
- 记录：增量事件 ID + 时间戳 + 来源 + 1-2 句摘要

**不做的**：
- 不重新读整份研报
- 不展开 background
- 不重写 thesis 段落

---

## 状态 2：filter-noise

**目标**：对低价值/重复/无日期/无来源信息降噪。

**分层**（按 `credibility_level`）：
- L1：官方披露（SEC/公司公告/工信部/IR）
- L2：官方行业（行业协会/监管/统计局）
- L3：权威媒体（财联社/路透/新华财经）
- L4：卖方研报（券商/研究机构）
- L5：市场定价 + 聚合数据（akshare/yahoo）
- L6：社交社区（X/雪球/股吧/抖音/小红书）

**内容类型**（`content_type`）：
- fact / forecast / company_claim / guidance / assumption / opinion / weak_signal / market_pricing

**研究角色**（`research_role`）：
- `technical` / `market_data` / `sellside_research` / `official_industry` / `social_sentiment` / `filing_news` / `macro_data` / `orchestrator`

**降噪规则**：
- 无日期 → 降级 L6
- 无来源 → 降级 L6
- 重复内容 → 合并
- 自媒体/小作文 → 默认 signal
- 谣言/无法验证 → 直接丢弃

---

## 状态 3：classify-incremental-claims

**目标**：把有效增量拆成 claim。

**8 类 claim_type**：

| claim_type | 含义 | 例 |
|---|---|---|
| `fact` | 已验证事实 | "5G-A 基站建成 100 万站" |
| `company_claim` | 公司口径 | "中际旭创 CEO 说 Q2 订单饱和" |
| `guidance` | 指引 | "公司指引 Q3 收入 +20%" |
| `forecast` | 预测 | "分析师预测 2026 年 CPO 出货 100 万件" |
| `assumption` | 假设 | "假设 800G 渗透率 2026 年达 30%" |
| `opinion` | 观点 | "我看好 CPO 是 5 年大趋势" |
| `weak_signal` | 弱信号 | "雪球 1 个帖子提到硅光延期" |
| `market_pricing` | 市场定价 | "008327 通信 ETF 今天涨 3%" |

**必标字段**：`claim_text` / `source_speaker` / `verification_status` / `as_of_date` / `confidence`

**回答 4 个问题**：
1. 新信息是 8 类 claim_type 中的哪一类？
2. 它是验证 / 削弱 / 替代 旧 thesis，还是只是噪音？
3. 它是否改变 evidence log 中某条 claim 的 `verification_status`？
4. 它是否触发 `stale_after` / `must_refresh_if` / 完整重研？

---

## 状态 4：write-monitor-log

**目标**：把有效增量写入当期监控记录。

**monitor-log 模板**：
```markdown
# Monitor Log: {主题} - {YYYY-MM-DD HH:MM}

## 当期增量
- claim 1（claim_type / source / as_of_date / confidence）
- claim 2
- claim 3

## 噪音丢弃
- 丢 1（原因）
- 丢 2（原因）

## 必标字段
- market_scope: A 股通信板块
- time_boundary: 2026-06-07 09:00 - 15:30
- depth_mode: quick_map
- primary_skill_or_loop: monitoring-loop
- routing_basis: 持续跟踪 008327 通信 ETF
```

---

## 状态 5：assess-impact

**目标**：判断增量是否改变 thesis / 风险 / 节奏 / 跟踪指标 / 当前框架 / 已选 overlay。

**5 维度评估**：
- **thesis impact**：`none` / `reinforcing` / `weakening` / `replacing`
- **risk impact**：`none` / `new_risk` / `risk_materialized` / `risk_faded`
- **pace impact**：`on_track` / `accelerating` / `decelerating`
- **framework validity**：`valid` / `questioning` / `invalid`
- **overlay validity**：`valid` / `no_longer_incremental` / `wrong`

---

## 状态 6：update-expectation-map

**目标**：把有效增量映射到 9 个预期变量。

**9 个预期变量**：
- revenue
- margin
- cash flow
- capex
- balance_sheet_risk
- valuation_multiple
- risk_premium
- positioning
- catalyst_timing

**A 股 ETF 标的的特殊变量**（可加）：
- 申赎资金流（份额变化）
- 折溢价率
- 跟踪误差
- 调仓换手率
- 板块轮动信号

**如果无法定位到变量** → 只能作为 watch item，**不能升级 thesis**。

---

## 状态 7：thesis-state-change

**目标**：判断是否更新 `thesis-ledger` 状态。

**7 种状态**：
- `draft` — 初始建立
- `active` — 主看法明确
- `watch` — 观察区（信号有但不构成行动）
- `upgrade_watch` — 催化临近（bullish 信号累积）
- `downgrade_watch` — 证伪累积（bearish 信号累积）
- `stale` — 过期（stale_after 到了）
- `retired` — 退出研究

**6 种状态转换**（允许的）：
```
active → upgrade_watch
active → downgrade_watch
active → stale
watch → active
watch → retired
downgrade_watch → retired
stale → active
```

**强约束**：**所有状态变化必须指向 evidence log 或 explicit source note** —— **不能无证据改状态**。

---

## 状态 8：escalate-or-close

**目标**：决定本期 monitor 走 escalate（升级）还是 close（关闭）。

**升级回 research-loop 的 9 条规则**（任一触发）：

1. 财报/指引/重大公告改变核心判断
2. 原 thesis 关键证据被削弱
3. 原 thesis 关键承诺没兑现 / 公司口径与已验证事实冲突
4. 原本是 `assumption / forecast / company_claim` 的关键输入被新证据证实/证伪
5. 重大事件改变公司/行业/估值叙事
6. 长期跟踪指标连续恶化
7. 标的的主导定价变量发生变化（原框架可能失效）
8. 原 overlay 的关键传导链被证伪/失去增量价值
9. 用户要求重做完整研究

**关 close 的 3 个条件**：
- 当期无有效增量
- 增量未达 `upgrade_watch` / `downgrade_watch` 阈值
- 跟踪指标全部 on_track

---

## Source Handling Rules（监控版）

- `official_and_industry` 优先进入核心更新检查
- `market_data` 用于价格/估值/技术面更新
- `web_read / web_search / public_api` 按需读取，**必须记录 ticker / series id / 参数 / 读取日期 / as-of date**
- `sellside_research` 走 `scan → recommend → approve → ingest`（不自动购买）
- `social_and_community` **默认作为 signal**（除非有明确证据链 + 可验证逻辑）
- `rumor` + `blocked` **不进入正式 monitor 结论**
- `market_pricing` 可用于判断市场反应和预期变化，**但不能替代基本面 claim**

---

## Output（monitor 产物）

每期 monitor 必须输出 7 件套：

```markdown
1. monitor summary             — 一句话总结当期增量
2. impact assessment           — 5 维度评估
3. expectation map update      — 哪些变量变化了
4. thesis state change decision — 状态是否变 + 证据指向
5. escalation decision         — escalate 或 close + 原因
6. framework still valid?      — 框架是否继续用
7. overlay still valid?        — overlay 是否继续用
```

---

## A 股 ETF 监控应用（008327 通信 ETF 案例）

| monitoring-loop 步骤 | 你的 cron agent.py 对应 |
|---|---|
| scan-updates | 抓 5 数据源（雪球/akshare/财联社/新浪/东财） |
| filter-noise | 关键词过滤（22 强 + 14 弱） |
| classify-incremental-claims | LLM 8 类 claim_type 打分（已实现） |
| write-monitor-log | 写入 monitor-log.md + evidence-log.csv |
| assess-impact | 影响反转信号（已实现） |
| update-expectation-map | 映射到 9 个预期变量（**待加**） |
| thesis-state-change | 7 状态机（**待加**） |
| escalate-or-close | next_action 字段（**待加**） |

**B 任务会把后 3 项（expectation-map / thesis-state-change / escalate-or-close）嵌进 agent.py**。
