# MCP 集成说明

## 使用的 MCP Server

| 项目 | 内容 |
|---|---|
| Server 名称 | 麦当劳中国 MCP（`mcd-mcp`） |
| 接入地址 | `https://mcp.mcd.cn/mcp-servers/mcd-mcp` |
| 协议 | Streamable HTTP |
| 鉴权 | `Authorization: Bearer ${MCD_MCP_TOKEN}`（Token 在 https://open.mcd.cn/mcp 申请，本地配置，不落仓库） |

## 使用的 Tool 与调用流程

日报生成流程（全部只读，顺序执行，单步失败降级为占位文案）：

```
now-time-info          → 取当前日期/星期（海报日期戳、运势种子）
campaign-calendar      → 当月活动 → 提取「今日」活动做情报区，主推活动进 hero 大图
query-my-coupons       → 用户卡包券 → 券名+折扣+临期标记进羊毛区（couponCode 脱敏不展示）
query-my-account       → 积分余额 → 雷达区（精确值脱敏为区间，如「15x 分」）
available-coupons      → 麦麦省可领未领 → 雷达区羊毛提醒
```

## 业务价值

把「查活动、查券、查积分」这三个用户每周要手动点 App 的动作，压缩成一句「生成麦门日报」+ 一张可分享海报：

- **真实价值**：临期券标红（防过期浪费）、可领券雷达（防漏薅）、活动播报（防错过联名）
- **传播价值**：日期种子决定的运势签当天全网一致，天然带「对答案」社交话题性
- **安全价值**：流程显式禁止写操作（领券/下单/抽奖），凭证字段强制脱敏，娱乐区与真实数据区视觉隔离
