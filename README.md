# vipthink-whatsapp-bot

> 欢乐童年（VIPTHINK）港澳/海外业务 — WhatsApp 智能续费助手
>
> 基于 SaleSmartly BSP + LLM，实现 WhatsApp 渠道的 AI 自动回复、个性化续费触达、家长档案管理

[![Status](https://img.shields.io/badge/status-P0%20MVP%20verified-brightgreen)](CHANGELOG.md)
[![Python](https://img.shields.io/badge/python-3.12+-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## 架构概览

```
家长 WhatsApp → SaleSmartly → Webhook → Worker → LLM → 回复
                                       ↓
                                 五维家长档案
                                (JSON + SQLite)
```

**五层架构**: 触达层 → API接入层 → 转化策略层(规则罩+LLM芯) → AI引擎层 → 数据运营层

详见 [方案设计文档](docs/WhatsApp_Bot_方案设计_v2.md)

---

## 快速开始

### 1. 环境要求

- Python 3.12+
- SaleSmartly 账号（Max版，已配置 WhatsApp 渠道）

### 2. 安装

```bash
git clone <repo-url>
cd vipthink-whatsapp-bot
pip install -r src/requirements.txt
```

### 3. 配置

```bash
cp src/.env.example src/.env
# 编辑 .env 填写:
#   OPENAI_API_KEY=sk-xxx       # LLM API Key
#   SS_API_TOKEN=xxx            # SaleSmartly API Token
#   SS_PROJECT_ID=gwl5qm        # SaleSmartly 项目ID
```

### 4. 启动

```bash
cd src
python ss_webhook_worker.py
# Worker 启动在 http://localhost:8089
```

### 5. 在 SaleSmartly 后台配置 Webhook

SS后台 → 自定义机器人 → Webhook URL: `http://你的服务器IP:8089/webhook/ss`

---

## 核心能力

| 模块 | 文件 | 说明 |
|------|------|------|
| Webhook Worker | `src/ss_webhook_worker.py` | 接收 WhatsApp 消息 → LLM 生成 → 同步返回 |
| API Client | `src/ss_api_client.py` | SaleSmartly API 鉴权 + 客户/会话/消息读取 |
| 家长记忆 | `src/parent_memory.py` | 五维动态档案（L1 JSON + L3 SQLite） |
| 种子数据 | `src/seed_parents.py` | 4种家长类型（主动续费/犹豫观望/流失倾向/忠实） |
| 闭环测试 | `src/test_webhook.py` | 多轮对话 + 安全规则罩验证（7/7 通过） |

---

## 项目结构

```
vipthink-whatsapp-bot/
├── src/                          # 源代码
│   ├── ss_webhook_worker.py      # FastAPI Webhook 主服务
│   ├── ss_api_client.py          # SaleSmartly API 客户端
│   ├── parent_memory.py          # 五维家长档案管理
│   ├── seed_parents.py           # 种子数据脚本
│   ├── test_webhook.py           # 闭环测试脚本
│   ├── config.json               # 配置文件
│   ├── .env.example              # 环境变量模板
│   ├── requirements.txt          # Python 依赖
│   └── start.bat                 # Windows 一键启动
├── docs/                         # 文档
│   ├── WhatsApp_Bot_方案设计_v2.md   # 完整方案设计
│   └── WhatsApp_Bot_方案设计_v2.html # HTML 美观版
├── README.md
├── CHANGELOG.md
├── .gitignore
└── LICENSE
```

---

## 关键技术决策

| 决策 | 方案 |
|------|------|
| BSP服务商 | SaleSmartly（自定义机器人 API） |
| LLM引擎 | OpenAI 兼容接口（可切 DeepSeek/Kimi） |
| 主动触达 | 3+1漏斗模型（T1窗口/T2预警/T3事件 + 安全兜底） |
| 续费优化 | 四支柱（服务35% + 效果25% + 时机25% + 门槛15%） |
| 个性化 | 五维数字孪生 + LLM动态生成 |
| 安全策略 | 规则包围LLM（代码粗筛 → LLM排序 → 代码兜底） |

---

## 借鉴项目

- [b2b-sdr-agent-template](https://github.com/iPythoning/b2b-sdr-agent-template) — 4层防遗忘记忆系统、ICP动态打分、消息调度

---

## License

MIT © VIPTHINK 港澳/海外 LP 团队
