# Changelog

## v0.1.0-alpha (2026-06-09)

### Added
- ✅ `ss_webhook_worker.py` — FastAPI Webhook 主服务，接收 SS 自定义机器人回调
- ✅ `ss_api_client.py` — SaleSmartly API 客户端，支持 external-sign 签名鉴权
- ✅ `parent_memory.py` — 五维家长档案管理（L1 JSON + L3 SQLite + L4 快照）
- ✅ `seed_parents.py` — 4种家长类型种子数据（主动续费/犹豫观望/流失倾向/忠实）
- ✅ `test_webhook.py` — 闭环测试脚本（7/7 全部通过）

### Verified
- ✅ SS API 连接验证：70,774 客户，263 WhatsApp-API 客户
- ✅ Webhook 闭环：接收 → 档案 → LLM → 返回，全链路跑通
- ✅ 安全规则罩：退费/投诉关键词代码层拦截（不可绕过）
- ✅ 签名算法：external-sign = MD5(token&sorted_params) 验证通过

### Design
- 📄 五层架构 v2（新增转化策略层）
- 📄 3+1漏斗主动触达模型
- 📄 四支柱续费优化（服务35% + 效果25% + 时机25% + 门槛15%）
- 📄 五信号打分引擎（紧急度40% + 意向30% + 活跃15% + 满意10% + 历史5%）
- 📄 规则包围LLM：代码粗筛 → LLM排序 → 代码兜底

### Pending
- ⏳ SaleSmartly 后台配置 Webhook URL + WhatsApp 渠道绑定
- ⏳ 接入真实 LLM API Key（当前使用模拟回复）
- ⏳ P0-3 WhatsApp 消息模板审核
- ⏳ P2 3+1漏斗主动触达引擎
