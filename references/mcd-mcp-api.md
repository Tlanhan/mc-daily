# 麦当劳中国 MCP 工具速查

> 官方仓库：https://github.com/M-China/mcd-mcp-server
> 开放平台：https://open.mcd.cn/mcp
> 协议：Streamable HTTP，鉴权 `Authorization: Bearer <MCP_TOKEN>`

## 日报用到的工具（全部只读）

### now-time-info
无参数。返回 `data.formatted`（yyyy-MM-dd HH:mm:ss）、`data.dayOfWeek`（如 FRIDAY）、`data.date`、`data.month`、`data.day`。

### campaign-calendar
- 不传参：返回当前月所有活动
- 传 `specifiedDate`（yyyy-MM-dd）：返回该日 + 前后最近各一个有活动的日子（共3天），适合聚焦"今日"
- 返回结构：按日期分组的活动列表，每条含活动标题、介绍文本、官方图片 CDN URL

### query-my-coupons
- 参数：`page`（字符串，默认1，最多5页）、`pageSize`（字符串，默认200，最大200）
- 返回每张券：券名、优惠金额（如"¥9.9 用券价格"）、有效期、标签（今日到期/到店专用/外送专用）
- ⚠️ 响应中含图片签名 URL（带 sign 参数），海报中可引用图片但不得展示签名参数本身

### query-my-account
返回 `data`：`availablePoint`（可用积分，字符串）、`accumulativePoint`（累计）、`currentMouthExpirePoint`（本月将过期，注意官方字段拼写就是 Mouth）、`nextMouthExpirePoint`（下月将过期）。

### available-coupons
无参数。返回麦麦省可领取券列表，每条含券名与状态（已领取/可领取）。用于"羊毛雷达"区块。

## 明确禁止日报调用的写操作工具

`auto-bind-coupons`（领券）、`mall-create-order`（积分兑换下单）、`create-order`（点餐下单）、`draw-lottery`（抽奖）、`cancel-order`、`party-order-create`、`delivery-create-address`。

除非用户在当前对话中明确要求执行对应操作，否则日报流程一律不触碰。

## 其他注意事项

- 限流：600 次/分钟，日报 5 次调用绰绰有余
- 只支持中国大陆地区（不含港澳台）
- 服务端时间即北京时间（GMT+8）
