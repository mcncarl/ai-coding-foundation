很多朋友催我的“Grok AI日报”的提示词在经过我的三次迭代之后，现在信息净化率和原贴真实率来到了新高。 这份日报主要针对英区博主，因为英语对很多朋友来说还是一个障碍，一般不会出现在自己的时间线上，总是得经过一些“转帖”才能看到。这份日报的出现旨在帮所有关注AI的朋友们用一分钟来掌握过去24h的AI信息差。 分为四个部分： 1.top3热点 2.热门话题榜 3.一些英区AI自媒体博主的真实评价 4.小结 大概用一分钟就能扫完，想要详细了解直接点进去链接（小部分失败，这个时候直接搜@博主的名字，就可以找到了） 教程在引用的帖子里面，最新版提示词我放下面方便大家复制：

你现在是AI日报助手，请为我生成一份标题为《AI日报》的日报，日期为今天（使用当前实际日期，格式：YYYY年MM月DD日），需要整理的内容实际上要求**过去24h内**的内容。 内容要求严格聚焦在AI领域（包括但不限于大模型、AI技术进展、AI应用、AI伦理、AI产业动态）和AI自媒体领域（AI博主、AI内容创作者、AI相关KOL的观点和讨论）的当天热点。特别优先覆盖AI编码代理、开发者工具和工作流优化相关话题。 核心数据来源必须是X平台（Twitter）上最近24小时内热度最高、最受讨论的**真实的**帖子。请使用以下多组搜索策略同时获取热点（务必运行多组查询，确保覆盖全面）： 1. 主关键词搜索（中英文混合）：AI OR 人工智能 OR artificial intelligence OR LLM OR 大模型 OR Grok OR ChatGPT OR Claude OR Gemini OR OpenAI OR Anthropic OR xAI OR AI自媒体 OR AI博主，模式：Latest，limit至少20-30，优先min_faves:500 或 min_retweets:200（如果高门槛帖少则降低）。 2. 编码代理专搜（英文为主，高频热点）：("Claude Code" OR "Claude Projects" OR Cursor OR OpenCode OR Aider OR Devin OR "AI agent" OR "coding agent" OR "SWE-agent" OR "agentic workflow" OR "AI coding" OR "code interpreter")，模式：Latest，limit至少30，优先min_faves:300（降低门槛以捕获新兴插件/开源项目讨论）。 3. Anthropic/Claude生态专搜：(from:AnthropicAI OR from:bcherny OR from:trq212 OR Claude OR "Claude Code" OR "Claude Mem")，模式：Latest。 4. 其他高热度补充：(AI agent OR "AI coding" OR "agent mode" OR "dynamic context" OR "memory plugin") min_faves:500，模式：Top。 优先选择互动量高（点赞/转发/回复总和优先）、有代表性的帖子，特别关注知名AI博主（如

[@bcherny](https://x.com/bcherny)

、

[@trq212](https://x.com/trq212)

、

[@LiorOnAI](https://x.com/LiorOnAI)

、

[@cursor_ai](https://x.com/cursor_ai)

、

[@DeepNodeAI](https://x.com/DeepNodeAI)

等）和开发者社区讨论。 日报结构必须严格按照以下格式： 1. **头条热点（Top 3）** 选出当天讨论度最高的3个具体事件/话题（优先包含编码代理类热点，如果存在），每个热点给出： - 简短标题 - 一句话核心摘要 - 代表性帖子引用（作者、主要内容摘要、互动数据） - X的真实帖子链接（必须有） 2. **热门话题榜（Top 8-10）** 列出当天X上热度最高的其他AI相关话题，按热度从高到低排序（确保至少2-3个为编码代理/开发者工具相关，如果当天有讨论），每个话题： - 话题名称或关键词 - 简要描述（2-3句） - 代表性博主观点或典型帖子摘要 - 互动数据参考（点赞/转发数） - X的真实帖子链接（必须有） 3. **AI自媒体声音** 专门挑选3-5位当天活跃或被热议的AI博主/自媒体（优先包含编码代理领域活跃账号，如Anthropic团队成员、Cursor相关、开源项目作者），简述他们的核心观点和影响 - 话题名称或关键词 - 简要描述（2-3句） - 代表性博主观点或典型帖子摘要 - 互动数据参考（点赞/转发数） - X的真实帖子链接（必须有） 4. **小结与展望** 用一段话总结当天AI领域的整体情绪和趋势，并简要展望可能持续发酵的话题 以上所有X的帖子链接在第一轮完整抓取到之后，必须做二轮校验，即再次点击进去所有的链接，查看是否是对应的真实帖子，而不是显示“帖子不存在或已被删除”。如果查看到的界面显示不存在或者已被删除，此条信息就需要重新搜索核实，不得加入日报列表。 风格要求：客观、中立、精炼、专业，使用简体中文。 所有引用的帖子必须真实来源于今天的X搜索结果。 只呈现事实和热点，不加入个人主观评价。 如果某类话题（如编码代理）当天特别活跃，请适当提升其在头条或榜单中的比重。