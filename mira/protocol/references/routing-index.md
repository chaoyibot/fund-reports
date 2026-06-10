# Mira Routing Index — 任务路由表

> 来自 `~/mira/OPERATING_CONTRACT.md` 的 lazy loading map + 机器优先 router

## 任务 → Loop/Skill 路由

| task_mode | primary_loop_or_skill | 触发词 | load_gate |
|---|---|---|---|
| `onboarding` | START_HERE.md | `hi Mira` / `你好 Mira` / `Mira help` / `怎么用 Mira` | 始终加载 |
| `wake_word_identity` | MIRA.md | 任何含 "Mira" 的请求 | 始终加载 |
| `quick_map` | loops/research-loop.md (depth=quick_map) | `Mira, 看一下 X` / `quick_map X` | 短时网络可跳过 |
| `standard_research` | loops/research-loop.md | `Mira, 研究 X` | run check_updates.sh |
| `deep_dive_research` | loops/research-loop.md (depth=deep_dive) | `Mira 深度研究 X` / `Mira, 完整分析 X` | run check_updates.sh |
| `monitoring_update` | loops/monitoring-loop.md | `Mira, 更新 X` / `Mira, 监控 X` | 增量读 + thesis_ledger |
| `thesis_update` | loops/thesis-update-loop.md | `更新 thesis` / `thesis ledger` | 读 thesis-ledger |
| `earnings_event` | skills/earnings-report-analysis/SKILL.md | 财报/指引/业绩预告 | earnings analysis |
| `event_delta` | loops/event-delta-loop.md | 财报前后比较 | event-delta |
| `monitoring_loop` | loops/monitoring-loop.md | 持续监控 | monitoring |
| `position_review` | loops/position-review-loop.md | `复盘 X` / `X 持仓怎么看` | position register |
| `portfolio_review` | loops/portfolio-review-loop.md | PM 复盘 | portfolio register |
| `portfolio_construction` | loops/portfolio-construction-review-loop.md | 组合构建 | exposure review |
| `decision_quality` | loops/decision-quality-review-loop.md | 决策质量复盘 | postmortem |
| `methodology_review` | loops/methodology-research-loop.md | `这个方法靠谱吗` | methodology-card |
| `etf_listing` | skills/etf-listing-analysis/ | 新 ETF 上市/资金流异动 | T0/T1 signal |
| `etf_discovery` | skills/etf-listing-discovery/ | `找新 ETF` | candidate watchlist |
| `industry_concept` | skills/industry-concept-analysis/ | CPO/HBM/光模块/黄金/存储/液冷 | 8 件套 |
| `research_report` | skills/research-report-interpretation/ | 研报/PDF/rating change | ingestion layer |
| `sec_filing` | skills/sec-filing-analysis/ | SEC/10-K/财报深挖 | source note |
| `data_screen` | python3 -m mira_data screen | `筛选 X 维度` | SEC + Yahoo L5 |
| `data_technical` | python3 -m mira_data technical | `算技术指标` | Yahoo L5 + stdlib |
| `view_continuity` | loops/view-continuity-loop.md | 继续/保存/比较用户观点 | private/ state |
| `market_briefing` | loops/market-briefing-loop.md | 盘前/收盘/日报/周报 | market_scope |
| `mira_self_update` | scripts/mira_update.sh | `update mira` | 不先跑 check_updates |

## 4 道数据门（cross-cutting）

所有 task_mode 都要过这 4 道门：

1. **信息价值门**（data/question-expansion-lenses.md）— 最多 2 个 lens
2. **live-data 门**（data/live-data-source-policy.md）— time-sensitive 必走
3. **ingestion 门**（data/ingestion-layer.md）— 新文件/API 必走
4. **quant 门**（skills/data-analysis-quality-gate/SKILL.md）— 数值结论必走

## 5 档深度

- `quick_map` — routing + 关键来源 + 核心判断 + source gap + refresh trigger
- `standard` — routed package 必需文件
- `deep_dive` — 多轮 refine + peer + contrary + calculation + overlay
- `live_update` — 增量 monitoring
- `historical_example` — 案例学习

## source_class 路由（source taxonomy）

| source_class | claim_type 默认 | 优先级 |
|---|---|---|
| `official_filing`（SEC/公司披露） | `reported_metric` / `fact` | L2（最高） |
| `official_industry`（行业协会/IR/监管） | `company_claim` / `forecast` | L2-L4 |
| `public_api_macro`（BLS/FRED/BEA） | `fact` | L2 |
| `market_data`（Yahoo v8） | `market_pricing` | L5（不替代基本面） |
| `aggregator_fundamentals`（yfinance） | `reported_metric` (screening) | L5 |
| `sellside_research`（券商研报） | `forecast` / `company_claim` | L4 |
| `web_read`（新闻/网页） | 视情况 | L4-L5 |
| `web_search`（搜索结果） | 视情况 | L5 |
| `social_community`（X/论坛/短视频） | 默认 `signal`（除非有证据链） | L6 |
| `rumor` | — | **不进入正式结论** |
| `blocked` | — | **不进入正式结论** |

## 关键产品 vs private 边界

| 类型 | 存放位置 | 谁能改 |
|---|---|---|
| **产品协议**（loops/skills/templates） | `~/mira/` tracked | Mira 团队 |
| **公共案例**（cases/aapl-2026-04/） | `~/mira/cases/` | Mira 团队（de-identified） |
| **研究方法学**（memory/methodologies/） | `~/mira/memory/` | Mira 团队 |
| **用户私有观点** | `private/research/<OBJECT>/` gitignored | 用户自己 |
| **用户持仓/预算** | `private/portfolio/` gitignored | 用户自己 |
| **用户偏好** | `private/preferences/user-preferences.md` | 用户自己 |

**A 股 ETF 标的的研究**（008327 通信 ETF、022502 黄金 ETF、018956 中航机遇领航）—
- 走 `industry_concept` 路由（不直接套单票 framework）
- evidence log 用 A 股数据源（akshare、东方财富、雪球 CDP、新浪财经、东财研报）
- claim_type 仍按 Mira 标准（事实/公司口径/承诺/指引/预测/假设/观点/弱信号/定价）
