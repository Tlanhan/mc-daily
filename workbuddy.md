# WorkBuddy 开发过程记录

> 本文件为参加「WorkBuddy 专项奖励」提交的对话上下文说明。项目全程在 WorkBuddy（腾讯）中完成，以下为开发会话的完整还原。

## 开发环境

- 工具：WorkBuddy（腾讯）Agent 模式
- 日期：2026-10-09（单日完成接入、开发、调试、部署、报名）
- 已接入连接器：麦当劳 MCP（mcd-mcp）、GitHub（connector）

## 对话时间线（按实际会话顺序）

### 1. MCP 接入（约 18:00-18:15）

- 用户：「https://mcp.mcd.cn 接入这个mcp」
- WorkBuddy：WebFetch 探测端点确认是麦当劳中国官方 MCP（401 鉴权响应证明端点有效）→ 引导用户到 open.mcd.cn 申请 Token → 写入 `~/.workbuddy/mcp.json`（streamablehttp）→ 实测 initialize 握手成功（返回 `mcd-mcp v1.0.0`）→ tools/list 拉取 33 个工具确认可用

### 2. 连通性验证与数据探索（约 18:15-19:30）

- WorkBuddy 依次实测调用：`now-time-info`（时间）、`query-my-coupons`（拉到真实 14 张券，发现 2 张当日到期）、`auto-bind-coupons`（用户明确指示后领取 3 张）、`query-my-account`（积分 15x 区间）、`mall-points-products`（积分商城盘点，发现「低分档更划算」规律）、`query-lottery-info`（抽奖奖池）、`campaign-calendar`（当日活动：GD 联名、龙焰鸡腿堡、蓝莓爆爆珠麦旋风等 14 个活动）

### 3. 选题决策（约 19:30-20:00）

- 用户发来大赛公众号文章链接，WorkBuddy WebFetch 提取赛事信息 → GitHub 搜索已有参赛作品盘点（营养配餐/团餐规划/麦金卡回本等方向已有人做）→ 提出 4 个差异化方向 → 用户选定「麦门日报」（每日运势+真实羊毛海报）

### 4. Skill 开发（约 20:00-20:50）

- WorkBuddy 调用内置 skill-creator 初始化骨架 → 编写 SKILL.md（工作流：拉数据→生成运势签→注入海报模板→附社交文案）→ references/mcd-mcp-api.md（工具速查+禁止写操作清单）→ references/copywriting.md（麦门黑话文案库）→ assets/poster-template.html（9:16 竖版海报模板，麦当劳红黄配色）→ 用当日真实 MCP 数据生成第一份示例海报

### 5. 版式修复（约 20:50-22:20）

- 用户反馈「图片没整好吧」→ WorkBuddy 诊断两根因：① .poster 写死 aspect-ratio 裁掉底部内容 ② 图片全走外链 CDN 不稳 → 修复：改自适应高度、活动图 curl 本地化到 imgs/、新增 hero 主视觉区 → 同步修复模板和 SKILL.md 红线规则

### 6. 开源发布与报名（约 22:20-22:35）

- 推送前用户要求安全核查 → WorkBuddy 全仓库扫描（含 zip 包内扫描）确认无真实 Token → gh CLI 创建公开仓库 Tlanhan/mc-daily → git push 提交代码

### 7. v1.1 迭代（约 22:35-23:00）

- 用户要求加「小红书配文自动生成」+「QR 码区 + 每日轮换版式」→ WorkBuddy 实现：日期种子 mod 3 轮换三套配色、qrserver API 生成 QR 并本地化、SKILL.md Step 4 升级为小红书配文工作流 → Edge 无头截图（`msedge.exe --headless --screenshot`）生成 README 示例图 → 推送 GitHub

## WorkBuddy 在本项目中承担的角色

| 环节 | WorkBuddy 做的事 |
|---|---|
| MCP 接入 | 端点探测、协议确认、配置写入、握手验证 |
| 数据探索 | 实测 10+ MCP 工具，盘点工具能力边界 |
| 创意策划 | 竞品盘点（GitHub 搜已有参赛作品）、差异化选题建议 |
| 架构设计 | Skill 目录结构、工作流、安全红线（只读/脱敏） |
| 前端开发 | 海报 HTML/CSS 模板、版式修复、轮换配色、QR 区 |
| 文案系统 | 麦门黑话库、运势签算法、小红书配文模板 |
| 测试验证 | 真实 MCP 数据端到端生成示例海报 |
| 部署发布 | 安全扫描、GitHub 仓库创建与推送、README 示例图 |

## 佐证材料

- 本仓库 git 提交历史：全部提交发生在 2026-10-09，commit message 与会话时间线对应
- 示例海报 `assets/poster-demo.png`：由 WorkBuddy 调用 Edge 无头截图从当日真实 MCP 数据生成的海报产出
- 仓库内所有代码与文档均由上述 WorkBuddy 会话产出
