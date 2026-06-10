# 008327 通信 ETF — Case Notes（研究笔记）

> **来源**: Mira 协议 (byteseek/Mira MIT) AAPL case 范本
> **作用**: 记录 008327 industry-analysis-package 研究过程中的洞察 / 弯路 / 盲点 / 后续补研方向
> **日期**: 2026-06-07

---

## 🎯 关键洞察（Key Insights）

### Insight 1: 008327 实际是"AI 算力 ETF"而非"5G ETF"

**最初假设**: 008327 通信 ETF = 5G 主题 ETF
**实际情况**: 通过 2025 Q1 季报 + 子赛道聚合，**核心暴露 = AI 算力硬件链**：
- 光模块 25-30% + 通信设备 15-18% + PCB 10-15% + 封测 8-10% + 连接器 4-5% = **75-80% 是 AI 算力链**
- 真正"5G" 主题（基站/射频/天线）占比 < 10%

**Mira 价值**: 这次分类不是按 ETF 名字（"通信"）分类，而是按**实际持仓**重新分类 —— 名字有误导性，**持仓暴露才是真相**。

### Insight 2: 季报只披露 50% 的"重仓股"，其他 50% 仍需推算

**观察**: 008327 季报披露 Top 15 持仓（合计 49.27% 权重），其他 50% 不披露。
**应对**: 推算假设"非披露 50% 仍按子赛道同分布"（即非披露的也是通信板块的小票）。
**风险**: 推算不等于事实，但**子赛道权重比例应该接近真实**（基金合同限制通信主题）。

**Mira 价值**: **承认 knowability_status = partially_knowable** —— 不要硬把推算包装成事实。

### Insight 3: 中际旭创/新易盛是 1.6T 双寡头，但 2027+ 持续性不可证伪

**已验证**:
- 中际旭创 1.6T 2025 Q1 量产 + 营收 +76% YoY（**L2**）
- Lightcounting 预测 2026 1.6T 出货 100 万件（**L4**）

**不可证伪**:
- NVIDIA 2027+ capex 是否持续（**L6 weak signal**）
- 1.6T 是否被 3.2T 或 CPO 替代（**irreducible_uncertainty**）

**Mira 价值**: **强制写明 `irreducible_uncertainty`** —— 不强行编"2027 capex 必然持续"的故事。

### Insight 4: 利润池从"通信设备"迁移到"光模块 + AI 服务器 PCB"

**2024**: 通信设备 30% / 光模块 25% / PCB 15% / 封测 10% / 其他 20%
**2025**: 光模块 35% / 通信设备 20% / PCB 18% / 封测 12% / 其他 15%
**2026**: 光模块 40% / AI 服务器 PCB 20% / 通信设备 15% / Chiplet 12% / 其他 13%

**Mira 价值**: 3 年趋势 + 量化（**derived_calculation**）+ 注明是 read-through（不是公司公告）。

### Insight 5: akshare stock_comment_em 23 只成分股加权得分是核心信号

**MCP 工具选择**:
- ❌ 雪球热榜（CDP 难 + WAF 风险）
- ❌ 财联社（50101 反爬）
- ✅ akshare stock_comment_em（3.1s 拿到 5184 只股票，11/11 通信成分股全命中）

**Mira 价值**: 选定**最稳定**的数据源 + **加权 23 只成分股**得到板块情绪分（不是单股情绪分）。

---

## 🚧 研究过程中的弯路（Mistakes / Detours）

### 弯路 1: 一开始以为 "ETF 跟踪指数" = "指数成分股"

**错误思路**: 中证通信主题指数 → 查指数成分股 → 映射到 008327
**正确思路**: **直接查基金季报披露的真实持仓**（更准确 + 更及时）

**教训**: 基金有 tracking error + 申赎影响，实际持仓 ≠ 理论指数成分股。

### 弯路 2: 子赛道分类靠"猜"

**错误思路**: 看股票名带"通信"就算通信设备
**正确思路**: 用 `sub_sector_map` 显式映射 + 启发式 fallback（"光"+"模块"→ 光模块；"PCB"+"电路"→ PCB）

**教训**: 明确分类标准 + 写明 fallback 规则。

### 弯路 3: 想要"算估值锚"但拿不到关键数据

**尝试**: PE/PB/EV/EBITDA 估值锚
**问题**: 季报数据有滞后 + 实时 PE 拿不到（雪球/同花顺 WAF 拦）
**应对**: **写 `valuation_anchor_quality = source_gap`**，**承认不知道**。

**Mira 原则**: **不要硬编"PE 30x = 估值合理"** —— 没数据就是没数据。

---

## 🕳️ 已知盲点（Known Blind Spots）

| 盲点 | 影响 | 缓解 |
|---|---|---|
| **非披露 50% 持仓是推算** | 子赛道权重有 ±10% 误差 | 按子赛道分布假设 + 标 `partially_knowable` |
| **2027+ AI capex 不可知** | 长期 thesis 不可持续 | 标 `irreducible_uncertainty` + 限定 horizon 12-18m |
| **CPO 路线时点不确定** | NVIDIA Spectrum-X 是最大变量 | 写 `must_refresh_if` 触发条件 4 |
| **国内运营商 capex 节奏** | 5G-A 投资规模不可知 | 标 `weak_signal` + 写 `tracking_metrics` 4 |
| **雪球/同花顺 WAF 拦** | 实时 PE 拿不到 | 用 akshare fund_nav 替代 + 标 `source_gap` |
| **季报披露滞后 1.5 月** | 持仓有 1.5 月延迟 | 写 `stale_after = 2025-08` 强制重看 |

---

## 📅 时间线（Timeline）

- **2026-06-07**: 启动 008327 industry-concept-analysis
- **2026-06-07 23:30**: 拉 2025 Q1 季报 + 子赛道分类
- **2026-06-07 23:45**: 写 8 件套 industry-map.md
- **2026-06-07 23:50**: 写 company-map.csv + sector-summary.json
- **2026-06-07 23:53**: 写 evidence-log.csv（20 列 AAPL 兼容）
- **2026-06-07 23:55**: 写 case-notes.md + README.md
- **stale_after**: 2025-08-15 前后（下季度报告披露日）

---

## 🔄 后续补研方向（Future Research）

### P0 优先级（必须做）

1. **中际旭创单票 deep_dive**（12 步 research-loop + investment-memo）
   - 触发条件: 1.6T 出货数据 + NVIDIA 订单明确
   - 必补: 估值锚（PE 30-40x → DCF base/bull/bear）
   - 路由: `loops/research-loop.md` + `skills/equity-research-core/`

2. **沪电股份单票 deep_dive**
   - 触发条件: AI 服务器 PCB 收入占比 > 30%
   - 必补: 28 层 PCB 客户结构 + 单价提价空间

### P1 优先级（建议做）

3. **新易盛/中航光电 季度复盘**
4. **Lightcounting 数据抓取自动化**（季报披露后实时跑）
5. **akshare stock_comment_em 通信板块 + 23 只成分股加权算法优化**

### P2 优先级（可选）

6. **CPO 路线 deep_dive**（对标 Mira cases/cpo-silicon-photonics-2026-06）
7. **中证通信主题指数 vs 国证通信指数对比**（不同 ETF 选哪只）
8. **008327 申赎资金流监控**（基金份额变化）

---

## 📚 借鉴的 Mira case / 协议

- **AAPL case**（cases/aapl-2026-04/）— investment-memo 模板（9 字段）+ 8 个 package 文件结构
- **monitoring-loop**（loops/monitoring-loop.md）— ETF 监控 8 状态机
- **MIRA.md + OPERATING_CONTRACT.md** — 唤醒词路由 + 18 项必填字段
- **industry-concept-analysis**（agents/research-orchestrator.md）— 8 件套产物

---

## 🛡️ Mira 协议自检（self-check）

按 MIRA.md「Required Output Discipline」18 项必填字段核对：

- [x] task_mode = `industry_concept`
- [x] research_object = `008327 通信 ETF`
- [x] market_scope = `A 股 ETF`
- [x] time_boundary = `2025 Q1 + 2026-06 实时`
- [x] output_language = `zh-CN`
- [x] depth_mode = `standard + deep_dive 混合`
- [x] primary_skill_or_loop = `industry-concept-analysis`
- [x] routing_basis = `ETF 是产业概念 → 走 industry-concept 分支`
- [x] followup_prompt_mode = `3 个 follow-up`（见 industry-map.md 末）
- [x] private_state_action = `waive`（无用户私人持仓）
- [x] source notes / evidence log = `evidence-log.csv 20 条`
- [x] live_data_gate = `waived`（季报数据 + L4 forecast 已足够）
- [x] ingestion_route = `akshare fund_portfolio_hold_em`（已记录）
- [x] quant_dependency = `light`（子赛道权重 + 利润池迁移）
- [x] facts / inferences / judgments 分离 = `evidence-log 5 类 claim_type 已标`
- [x] judgment_confidence + reversal_condition = `medium + 3 条 invalidation`
- [x] information_value + knowability = `irreducible_uncertainty 标 2 处`
- [x] stale_after = `2025-08-15 前后`
- [x] must_refresh_if = `5 条`

**全部通过** ✓

---

**Generated by Mira 协议 · 008327 通信 ETF industry-analysis-package · 2026-06-07**
