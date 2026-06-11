# VIPTHINK WhatsApp Bot

> 港澳/海外 LP 团队 WhatsApp 智能助手  
> 基于三 Agent 架构 + 五维家长画像 + LLM 个性化生成

---

## 项目状态

| 阶段 | 状态 |
|------|:--:|
| 方案设计 | ✅ 完成（五轮攻防讨论） |
| P0 通道搭建 | 🔴 阻塞（SaleSmartly 下架自定义机器人功能） |
| P1 被动回复 | ⏳ 待 BSP 恢复 |
| P2 主动触达 | ⏳ 待 P1 完成 |
| P3 持续优化 | ⏳ 待 P2 完成 |

---

## 架构概览

### 三 Agent 体系

```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Agent 1    │  │   Agent 2    │  │   Agent 3    │
│  收信与路由   │  │  档案与画像   │  │  触达与运营   │
│  实时 · 战术  │  │  定时 · 数据  │  │  每日 · 战略  │
└──────┬──────┘  └──────┬──────┘  └──────┬──────┘
       │                │                │
       └────────┬───────┴────────────────┘
                │  共享数据层
        ┌───────┴───────┐
        │ JSON + SQLite │
        │  + 飞书表格    │
        └───────────────┘
```

| Agent | 职责 | 触发 | 状态 |
|-------|------|------|:--:|
| Agent 1: 收信与路由 | Webhook → 意图识别 → KB检索 → LLM生成 → 回复/升级 | 实时 | 代码已有，待接通 |
| Agent 2: 档案与画像 | 五维档案 CRUD + SmartBI同步 + 对话摘要 + 信任分 | 定时+事件 | 核心模块已有 |
| Agent 3: 触达与运营 | 3+1漏斗扫描 + LLM排序 + 频控 + 报表 + 质检 | 每日 | 待开发 |

### 五层架构（对应方案文档七）

```
L1 家长触达层 → 被动咨询 + 主动触达 (3+1漏斗)
L2 API 接入层 → SaleSmartly BSP (当前阻塞)
L3 转化策略层 → 规则安全罩 + LLM决策芯 + 四支柱优化
L4 AI 引擎层   → 意图识别 + 知识库 + 个性化引擎 + 人工升级
L5 数据运营层   → 五维档案 + 对话日志 + 续费看板 + 质检反馈
```

### 知识库接入

- 共享模块 `kb_client.py`（非独立 Agent）
- 复用现有 Karpathy LLM-Wiki 三层架构（sources → wiki → bot）
- 钉钉/WhatsApp 共享事实层 + channel 标签过滤
- 六触点工作流覆盖三个 Agent

---

## 目录结构

```
├── README.md                          ← 本文件
├── WhatsApp_Bot_方案设计_v2.md         ← 完整方案文档（1500+行）
├── WhatsApp_Bot_方案设计_v2.html       ← 方案文档 HTML 版
│
├── agent_1_inbound/                   ← Agent 1: 收信与路由
│   ├── main.py                        # Webhook 主服务 (FastAPI, :8089)
│   ├── ss_api.py                      # SS API 客户端
│   ├── tunnel.py                      # ngrok 隧道管理
│   ├── test_main.py                   # 多轮对话 + 安全测试
│   ├── config.json                    # 配置文件
│   ├── .env.example                   # 环境变量模板
│   └── requirements.txt               # Python 依赖
│
├── agent_2_profile/                   ← Agent 2: 档案与画像
│   ├── main.py                        # ParentMemory 五维档案引擎
│   ├── scheduler.py                   # Cron 调度器（定时任务入口）
│   ├── summarizer.py                  # 对话摘要生成器
│   ├── trust_calculator.py            # 信任分计算器
│   ├── personality_analyzer.py        # 人格画像分析器
│   ├── mapper.py                      # ID 映射 (chat_user_id → parent_id)
│   ├── quality.py                     # 质量审计
│   └── seed.py                        # 种子数据 (4种家长类型)
│
├── agent_3_outreach/                  ← Agent 3: 触达与运营
│   ├── main.py                        # 主入口 + Cron 调度
│   ├── scanner.py                     # 3+1漏斗触发引擎
│   ├── ranker.py                      # LLM 触达排序器
│   ├── sender.py                      # 批量发送器（安全罩 + 频控）
│   └── reporter.py                    # 飞书/钉钉报表推送
│
└── shared/                            ← 共享模块
    ├── safety_check.py                # 安全规则罩（三 Agent 共用）
    ├── kb_client.py                   # 知识库客户端
    ├── bad_case_db.py                 # Bad Case 存储
    └── reply_checker.py               # L1 回复质检
```

### 开发进度

| Phase | 内容 | 状态 |
|-------|------|:--:|
| Phase 1 | 目录重构（ss_worker → 四个目录） | ✅ |
| Phase 2 | 共享模块（safety_check + kb_client + bad_case_db） | ✅ |
| Phase 3 | Agent 2 开发（scheduler + summarizer + trust + personality） | ✅ |
| Phase 4 | Agent 3 脚手架（scanner + ranker + sender + reporter） | ✅ |
| Phase 5 | Agent 1 精炼（接入 kb_client + Few-Shot） | ⏳ 待 BSP 恢复 |

### 关联项目

| 项目 | 关系 |
|------|------|
| `vipthink-lp-assistant` | 共用知识库 + 路由引擎（钉钉 Bot） |
| `vipthink-renewal-intention` | 续费意向数据联动 |
| `smartbi-data-cli` | SmartBI 数据导出 |
| `lp-knowledge-base` | 知识库三层架构（sources/wiki/bot） |
| `knowledge/` | 业务知识文档（SOP/话术/规则/质检） |

---

## 关键设计决策

| # | 决策 | 日期 | 状态 |
|---|------|------|:--:|
| 1 | BSP 选型：SaleSmartly 自定义机器人 | 2026-05-30 | 🔴 待恢复 |
| 2 | 五层架构 + 四支柱续费优化 | 2026-05-30 | ✅ |
| 3 | 规则包围 LLM（代码粗筛→LLM排序→代码兜底） | 2026-05-30 | ✅ |
| 4 | 家长数字孪生（五维 JSON 档案） | 2026-05-30 | ✅ |
| 5 | 三 Agent 拆分（收信/档案/运营） | 2026-06-11 | ✅ |
| 6 | 知识库 = 共享模块 kb_client.py + channel 标签 | 2026-06-11 | ✅ |
| 7 | Agent 边界三原则（触发节奏/数据主责/故障域） | 2026-06-11 | ✅ |
| 8 | 质量管理：分布式 quality.py + 五层防线 | 2026-06-11 | ✅ |
| 9 | Agent 自迭代：Few-Shot / 权重校准 / A/B 测试 | 2026-06-11 | ✅ |

---

## 质量管理

### 五层防线

| L1 实时拦截 | L2 行为信号 | L3 回归测试 | L4 LP标注 | L5 策略评审 |
|:--:|:--:|:--:|:--:|:--:|
| reply_checker | 家长回复/投诉信号 | bad_case.db 变更验证 | 5秒快速标记 | 双周30min |

### Agent 自迭代

| Agent | 迭代机制 | 周期 |
|-------|---------|------|
| Agent 1 | 重复拦截→新增FAQ / Bad Case→Few-Shot注入 / LP标注→Prompt修正 | 每日+实时 |
| Agent 2 | SmartBI对比校准 / 权重重校准 | 每日+每月 |
| Agent 3 | 退订监控→降频 / A/B测试→策略推广 | 即时+双周 |

---

## 快速开始

### 前置条件

- Python 3.12+
- SaleSmartly 自定义机器人功能恢复
- LLM API Key（OpenAI 兼容接口）
- ngrok（本地开发隧道）

### 启动 Agent 1（当前可运行）

```bash
cd ss_worker
cp .env.example .env          # 编辑 .env 填入 API Key
pip install -r requirements.txt
python ss_webhook_worker.py    # 启动在 :8089
```

### 测试

```bash
python test_webhook.py         # 多轮对话 + 安全测试
```

### 健康检查

```bash
curl http://localhost:8089/health
# {"status":"ok","version":"vipthink-lp-assistant-v1",...}
```

---

## 运营红线

- Agent 间不互调 API，通过共享数据层解耦
- `safety_check()` 是所有发送前的必经之路（❌ 投诉/退费/冷却期内 → 阻断）
- Agent 2 挂了 → Agent 1 用旧档案降级运行
- Agent 3 挂了 → 被动回复完全不受影响
- 知识库只读，修改需走 `sources/ → wiki/ → bot/` 管道
- 方案文档为单一真相源，所有设计决策在此记录

---

## 维护者

**孙昊天** — VIPTHINK 港澳/海外 LP 团队  
项目日期：2026-05-30 至今
