# Xiaohongshu (RedNote) MCP server, no login

[中文](README.zh.md)

A hosted remote MCP server that lets your AI search RedNote notes, read their comments and see what an account posted lately. **No QR code, no cookies, no RedNote account of yours: your account stays out of it. It reads public content and does not post.**

This repository holds the setup, what each kind of question costs, and what it cannot do. The server is hosted by SocialToAI, so there is nothing here to install. Published by the SocialToAI team.

## Is this the right kind of server for you?

There are two common designs, and each suits different people.

| | Runs on your machine, uses your login | This hosted server |
| --- | --- | --- |
| Account | Your own account and cookies | None of yours |
| Install | A local program; log in again when the session ends | Nothing; one address |
| Can publish notes and interact | Yes | **No, read only** |
| Cost | The program is free | Credits per call, 12 and up on RedNote |

If you need your AI to publish for you, a local server is the right choice. If you want to research what people say without putting your own account in the loop, use this one.

## Connect

Address: `https://mcp.socialtoai.com/mcp`

```bash
# Claude Code: run this, then type /mcp, pick socialtoai and sign in
claude mcp add --transport http socialtoai https://mcp.socialtoai.com/mcp

# Codex: the browser opens for sign-in
codex mcp add socialtoai --url https://mcp.socialtoai.com/mcp
```

Cursor, ChatGPT, the Claude apps and others: [step by step](https://socialtoai.com/mcp/xiaohongshu/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106). Sign-in is to SocialToAI (an email code or Google) and has nothing to do with RedNote. New accounts get 100 free credits, no card.

## What to ask, and about what it costs

Credits checked on 2026-11-05 against [`/v1/capabilities`](https://api.socialtoai.com/v1/capabilities) (price version `pv_29229c6acba5`). Dollars are on the $10 pack (3,000 credits).

| Job | Ask something like | Call | Credits |
| --- | --- | --- | --- |
| What people say about a topic | "What are people on Xiaohongshu saying about camping coffee? Answer in English with links." | `brand_buzz` (small, 90 days) | 36 recorded ($0.12) |
| Which notes took off | "Which camping notes on Xiaohongshu did far better than their author's usual this month?" | `research` `viral_finder` | 54 recorded ($0.18) |
| A note's comments | "Read the comments on this note and separate complaints from people who want to buy." | `detail` + `comments` × 3 | 48 ($0.16) |
| Benchmark accounts | "Find five accounts that post about camping coffee and show me what they posted lately." | `search` + `user_posts` × 5 | 72 ($0.24) |
| Shop products and reviews | "Search the Xiaohongshu shop for camping lights and read the first page of reviews." | `xiaohongshu.product_search`, `xiaohongshu.product_reviews` | 12 each |
| Keyword interest | "Show the search index for 露营 over the last three months." | `xiaohongshu.keyword_history` | 24 |
| Creator data | "Who follows this creator?" | `xiaohongshu.kol_search`, `xiaohongshu.kol_audience` | 24 each |

100 credits is about 8 ordinary calls on RedNote: enough to try one or two real questions, not enough for bulk work.

## A recorded answer

Recorded on the live service on 2026-10-18; the whole run is on [the guide page](https://socialtoai.com/docs/guides/read-xiaohongshu-notes-on-a-topic/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106).

> **Asked:** What are people on Xiaohongshu saying about camping coffee?
>
> **Call:** `brand_buzz` with `brand: "露营咖啡"`, `about: "户外露营时自己做咖啡"`, `window: "90d"`, `scale: "small"`: 13 notes.
>
> **Answer, in short:** 8 of the 13 are really about it, all positive. Features come up most (38%, 3 notes), then ease of use (25%, 2) and quality (13%, 1). Each point links to its note. The sample is small: read it as a direction, not a measurement.
>
> **Spent:** 36 credits, $0.12.

## Three practical notes

1. **Pass links whole.** The `xsec_token` in a RedNote link is what opens it. Use the links from results as they come, and tell your AI not to tidy them.
2. **Search in Chinese.** Content is in Chinese. Ask in English if you like, but have your AI search with Chinese keywords and translate the results.
3. **Set a budget.** Ready-made tools quote first, and with `max_cost_credits` a call over budget is refused for free.

## Limits

- **Read only.** No publishing, comments, likes, follows or messages.
- **Public content only.** Nothing behind a login, and no feed of your own.
- **Fields it cannot give:** note views, reposts, hashtags and pinned status, plus author follower counts in `search` results, come back as `null`. Your AI says "not given" rather than guessing.
- **Page sizes:** about 10 comments and 5 replies a page; each page is charged as a call.
- **Commenters are anonymous** in every answer.
- Numbers are as of the moment they were read. Follow the platform's rules and the law that applies to you when you use the data.

## Links

- [RedNote MCP page, with every call](https://socialtoai.com/mcp/xiaohongshu/?utm_source=github&utm_medium=repo&utm_campaign=xiaohongshu-mcp-20261106)
- [All 18 platforms](https://github.com/socialtoai/socialtoai-mcp)
- Support: support@socialtoai.com

MIT licence for the text and examples in this repository. Published by the SocialToAI team.
