# 麦门日报 McDoor Daily 🍟

> 基于麦当劳中国 MCP 的每日运势情报海报生成器 —— 运势是娱乐，羊毛是真情。

![麦门](https://img.shields.io/badge/%E9%BA%A6%E9%97%A8-%E6%B0%B8%E5%AD%98-c0392b) ![MCP](https://img.shields.io/badge/McDonald's%20China-MCP-ffbc0d)

## 这是什么

每天一张竖版社交海报（9:16），包含：

- 🎰 **今日麦门运势签**——固定规则由日期决定（当天所有人结果一致，方便"对答案"），宜/忌从当日真实活动与券的到期情况反推
- 📰 **今日麦门情报**——来自 `campaign-calendar` 的真实活动（新品、联名、优惠）
- 🎫 **卡包羊毛**——来自 `query-my-coupons` 的真实在库券，临期券标红
- 📡 **羊毛雷达**——来自 `query-my-account` / `available-coupons` 的积分与可领券提示

## 使用方法

1. 前置：已接入麦当劳中国 MCP。配置方法见 [M-China/mcd-mcp-server](https://github.com/M-China/mcd-mcp-server)，Token 在 https://open.mcd.cn/mcp 申请。
2. 安装本 Skill（导入 `mc-daily.zip` 或克隆本仓库到技能目录）。
3. 对 WorkBuddy 说：**"生成今日麦门日报"**。
4. 得到 `麦门日报-YYYY-MM-DD.html`，可直接截图分享朋友圈/小红书。

## 设计原则

- **只读安全**：日报流程只调用查询类工具（`now-time-info` / `campaign-calendar` / `query-my-coupons` / `query-my-account` / `available-coupons`），绝不触发领券、下单、抽奖等写操作。
- **数据脱敏**：海报不展示 couponCode、accountId 等凭证字段；积分显示为区间。
- **真实数据**：羊毛信息全部来自 MCP 实时接口，禁止编造；运势签为纯娱乐并与真实信息视觉分区。

## 参赛声明（2026 麦当劳程序员创意开发大赛）

- 本项目为个人非商业作品，基于麦当劳中国官方开放的 MCP 服务开发。
- 未将任何真实 MCP Token、账号凭证写入本仓库；使用前需用户自行申请 Token 并本地配置。
- 麦当劳及相关商标权利归 McDonald's Corporation 所有，本项目与其无隶属关系。

## License

MIT（代码）；文案与"麦门"梗仅用于社区娱乐。
