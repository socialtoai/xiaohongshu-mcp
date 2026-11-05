# 小红书 MCP：不用登录，让 AI 读小红书笔记和评论

[English](README.md)

托管的远程 MCP，让 AI 搜笔记、读评论、看账号最近发了什么。**不用扫码，不用 Cookie，不用你的小红书账号，不会牵连你的账号。只读公开内容，不发帖。**

这个仓库放的是配置方法、哪种问题花多少积分、以及它做不到的事。服务由 SocialToAI 托管，这里没有需要安装的东西。SocialToAI 团队发布。

## 先看适不适合你

小红书 MCP 大致有两种做法，各有适合的人：

| | 本机跑、用你的登录态 | 这个托管服务 |
| --- | --- | --- |
| 账号 | 用你自己的账号和 Cookie | 不用你的账号 |
| 安装 | 本机装程序，登录失效要重来 | 不装，贴一个地址 |
| 能发笔记、能互动 | 能 | **不能，只读** |
| 费用 | 程序免费 | 按次付积分，小红书每次 12 积分起 |

要让 AI 替你发笔记，选本机方案。只想读内容、做调研、不想把自己的账号放进去，用这个。

## 接入

地址：`https://mcp.socialtoai.com/mcp`

```bash
# Claude Code：运行后输入 /mcp，选 socialtoai，登录
claude mcp add --transport http socialtoai https://mcp.socialtoai.com/mcp

# Codex：浏览器会打开登录页
codex mcp add socialtoai --url https://mcp.socialtoai.com/mcp
```

Cursor、Trae、Cherry Studio 等客户端的配置见 [接入页](https://socialtoai.com/zh/mcp/xiaohongshu/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106)。这里的登录是登录 SocialToAI（邮箱验证码或 Google），和小红书账号无关。新账户送 100 积分，不用绑卡。

## 能问什么，大概多少积分

积分是 2026-11-05 查询 [`/v1/capabilities`](https://api.socialtoai.com/v1/capabilities) 得到的（价格版本 `pv_29229c6acba5`），美元按 $10 档 3,000 积分算。

| 任务 | 可以这样问 | 调用 | 积分 |
| --- | --- | --- | --- |
| 一个话题大家怎么说 | “小红书上露营咖啡的口碑怎么样？按优缺点归类，每条带原笔记” | `brand_buzz`（small，近 90 天） | 录制 36（$0.12） |
| 这个月哪些笔记跑出来了 | “这个月小红书上哪些露营笔记明显比作者平时好？” | `research` `viral_finder` | 录制 54（$0.18） |
| 读一篇笔记的评论 | “读这篇笔记的评论，把吐槽和想买的人分开” | `detail` + `comments` × 3 | 48（$0.16） |
| 看对标账号 | “找 5 个讲露营咖啡的账号，看他们最近发了什么” | `search` + `user_posts` × 5 | 72（$0.24） |
| 比两个词 | “这周小红书上手冲和挂耳哪个讨论多？” | `search` × 2 | 24（$0.08） |
| 店铺商品和买家评价 | “搜小红书店铺的露营灯，读第一页买家评价” | `xiaohongshu.product_search`、`xiaohongshu.product_reviews` | 各 12 |
| 关键词热度 | “露营这个词最近三个月的搜索指数” | `xiaohongshu.keyword_history` | 24 |
| 博主后台数据 | “这位博主粉丝是什么人？” | `xiaohongshu.kol_search`、`xiaohongshu.kol_audience` | 各 24 |

100 积分在小红书上大约是 8 次普通调用，够跑通一到两个真实问题，不够批量。[价格页](https://socialtoai.com/zh/pricing/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106)有全部价格。

## 一段真实录制

2026-10-18 在线上真实跑的，完整过程见[指南页](https://socialtoai.com/zh/docs/guides/read-xiaohongshu-notes-on-a-topic/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106)。

> **问：** 小红书上大家怎么说露营咖啡？
>
> **调用：** `brand_buzz`，`brand: "露营咖啡"`，`about: "户外露营时自己做咖啡"`，`window: "90d"`，`scale: "small"`，返回 13 篇笔记。
>
> **答案要点：** 13 篇里 8 篇真的在讲这件事，全部偏正面。提得最多的是功能（38%，3 篇），其次是方便（25%，2 篇）和品质（13%，1 篇）。每一点都能点回原笔记。样本小，只能当方向看。
>
> **花费：** 36 积分，$0.12。

## 三个实用提示

1. **链接要完整传。** 小红书笔记链接里的 `xsec_token` 是打开它要用的参数，删掉可能就打不开。搜索结果给的链接直接用，别让 AI “清理”它。
2. **搜中文。** 小红书是中文内容，英文问题也请 AI 用中文关键词搜，再翻译结果。
3. **先小后大。** 现成工具会先报价；设了 `max_cost_credits` 后，超过预算的调用不会执行，也不收费。

## 限制

- **只读。** 不能发笔记、评论、点赞、关注或私信。
- **只读公开内容。** 不碰需要登录才能看的东西，也没有你的首页推荐。
- **这些字段拿不到：** 笔记浏览量、转发数、作者粉丝数（在 `search` 的结果里）、话题标签、是否置顶，都返回 `null`。AI 会说“没有提供”，不会估一个数。
- **一页条数有限：** 评论每页约 10 条，回复每页约 5 条，读得多就要翻页，每页都按一次收费。
- **评论者匿名。** 回答里不出现评论者的名字。
- 数字是读取那一刻的值。用小红书数据做商业决定或发布内容时，请遵守平台规则和相关法律。

## 每天盯一个账号或关键词

对 AI 说“每天早上帮我看这个账号有没有新笔记”，它先报价，你同意才建。小红书每天一次 12 积分，一个月最多 372 积分（约 $1.24）。新内容发邮箱或飞书、企业微信群，也可以放进 RSS 阅读器。免费账户可留 1 个监控。按天或按周运行，不是实时。

## 链接

- [小红书 MCP 页面，含全部调用](https://socialtoai.com/zh/mcp/xiaohongshu/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106)
- [全部 18 个平台](https://github.com/socialtoai/socialtoai-mcp)
- 支持：support@socialtoai.com

本仓库的文字和示例采用 MIT 许可。SocialToAI 团队发布。
