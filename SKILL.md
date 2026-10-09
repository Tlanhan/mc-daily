---
name: mc-daily
description: 麦门日报生成器。当用户想要"生成麦门日报"、"今日麦麦运势"、"麦当劳今日活动海报"、"麦门黑话运势签"或要求把麦当劳 MCP 的活动/优惠券/积分数据做成可分享的社交海报时使用。基于麦当劳中国 MCP（mcd-mcp）真实数据，产出竖版 HTML 海报（9:16，适合朋友圈/小红书/微博分享）。触发词：麦门日报、麦麦日报、麦门运势、麦当劳海报。
agent_created: true
---

# 麦门日报 McDoor Daily

## Overview

基于麦当劳中国 MCP 的真实数据（活动日历、用户优惠券、积分账户、新品信息），生成一张竖版社交海报：**今日麦门运势签 + 真实活动情报 + 真实可用优惠券**。定位是"好玩、可分享、有真实羊毛可薅"的每日轻内容。

核心价值：运势签是娱乐外壳（社交传播点），活动+券是真实价值（留存点）。

## 前置条件

- 已接入麦当劳中国 MCP（连接器名 `mcd-mcp`，Streamable HTTP，端点见 references/mcd-mcp-api.md）
- 用户已完成 MCP Token 配置；若未配置，引导用户到 https://open.mcd.cn/mcp 申请

## 工作流

生成一份日报按以下顺序执行：

### Step 1: 拉取数据（全部只读，禁止任何写操作）

按顺序调用 MCP 工具，任何一步失败就跳过并在海报对应区块显示"今日数据休息中"：

1. `now-time-info` — 获取当前日期（海报日期与星期）
2. `campaign-calendar`（不传参，取当月）— 活动情报
3. `query-my-coupons` — 用户卡包券（海报只展示券名+折扣，不展示 couponCode 等敏感字段）
4. `query-my-account` — 积分余额与临期提醒
5. `available-coupons` — 可领未领的券（生成"羊毛雷达"区块）

⚠️ 安全红线：日报生成过程是**只读**的。绝不调用 `auto-bind-coupons`、`create-order`、`mall-create-order`、`draw-lottery` 等任何写操作工具，除非用户在当次对话中明确指示。

### Step 2: 生成运势签（娱乐层）

运势签规则（固定 6 种，用日期做种子保证当天所有人结果一致，增加"对答案"的社交话题性）：

| 运势 | 判定（按日期单双） | 文案方向 |
|---|---|---|
| 大吉 | 日期为偶数 且 星期数为奇数 | "麦门永佑你" |
| 吉 | 日期为偶数 | "今日宜吃麦" |
| 小吉 | 星期五 | "周五麦门赦免日" |
| 平 | 其他 | "麦门中立，谢谢薯条" |
| 小凶 | 日期为奇数 且 含数字3 | "麦门在渡劫，吃份麦乐鸡压压惊" |
| 凶 | 日期为奇数 | "建议吃麦门冷静一下" |

宜/忌清单从当日真实数据反推生成：
- **宜**：从活动里提取（如"宜尝新""宜打卡 GD 联名"）
- **忌**：从券的到期情况反推（如有今日到期券 → "忌犹豫（有券今日到期）"）

### Step 3: 生成海报

用 `assets/poster-template.html` 作为骨架（已含完整 CSS，勿改版式），按模板内 `<!-- DATA -->` 注释锚点注入数据。产出为单个自包含 HTML 文件（内联全部样式）。

**每日版式轮换**（与运势签共用日期种子，保证当天一致性）：日期数字和 mod 3 决定当日版式：

| 结果 | 版式名 | 配色（改 .poster 的 background） |
|---|---|---|
| 0 | 朝阳橙黄 | `linear-gradient(160deg,#ffbc0d,#ff8a00 45%,#e23a2e)` |
| 1 | 麦夜红棕 | `linear-gradient(160deg,#5c1a1a,#8e2020 45%,#3d0c0c)`（footer/qr-text 保持白字） |
| 2 | 薯条金黄 | `linear-gradient(160deg,#ffd54d,#ffc72c 45%,#ff8a00)`（正文卡片区不变，仅背景变） |

**QR 码区**（footer 上方）：用公开 QR API 生成（如 `https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=<URL>`），链接指向麦当劳 App 下载页或当日主推活动页；同样用 curl 下载到 `imgs/qr.png` 本地引用。QR 引导文案写当日最佳行动的短句（如「扫码领同款」「今晚到期，速用」）。

- 输出路径：`<工作目录>/麦门日报-YYYY-MM-DD.html`，**同时在同目录创建 `imgs/` 文件夹，将海报用到的活动图片和 QR 图用 curl 下载到本地并在 HTML 中以相对路径引用**（不依赖外链 CDN，防加载失败/防盗链/网络差异）
- 版式红线：**不要给 .poster 写死 aspect-ratio 或固定高度**（每天内容长度不同，会裁掉底部区块）；footer 用 `margin-top: 14px` 而非 `margin-top: auto`
- 每天选 1 个主推活动放大图放 hero 区（宽 100%），其余活动用 72px 缩略图列表
- 用 present_files 展示给用户

### Step 4: 交付文案（小红书配文自动生成）

海报完成后，必须附一段**可直接复制到小红书**的配文（不是泛泛的社交文案）。按 references/copywriting.md 的「小红书模板」产出：emoji 密、口语化、带话题标签、正文含真实羊毛信息钩子，结尾附 5-8 个话题标签。

## 数据脱敏规则（强制）

海报中禁止出现：
- couponCode / couponId / accountId / traceId 等任何凭证类字段
- 用户积分精确余额（显示为区间，如"15x 分"）
- 手机号、地址等个人信息

## 参考文件

- `references/mcd-mcp-api.md` — MCP 工具清单与响应字段说明
- `references/copywriting.md` — 麦门黑话文案库与社交文案模板
- `assets/poster-template.html` — 海报 HTML 骨架（9:16 竖版，含内联 CSS）
