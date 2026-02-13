![](NotebookLM%20Mind%20Map.png)

每个Skill文件夹里可以包含：

- SKILL.md（核心指令文件，必需）
    
- scripts/（可执行脚本，可选）
    
- references/（参考文档，可选）
    
- assets/（模板和资源，可选）

 每一个skill的md文档里面包含**元数据（metadata）、指令（instructions）和资源（resources）【元数据里面的描述（description）一定要比较详细，否则AI也不容易抓取到】

resources又包含：
- reference（参考文档）、script（脚本）、assets（模版）
- 渐进式披露：每次给AI指令的时候，AI**必读metadata、instructions**，按指令的**具体需求来读reference、script和assets**。

1. **reference**就是放在另外一个md文档里面的专门事项说明书，只有明确指令的时候才加载（占上下文）
2. **script**就是一个脚本，AI执行的时候只管输入和输出，不会管具体内容（不占上下文）
3. **assets**是最终AI输出结果需要包含的内容，但是AI不会管它具体是什么，只管把它放到最后的结果，比如一些品牌logo，样板代码。（不占上下文）



 # MCP vs Skills vs Subagent核心区别

  ## 1.MCP (Model Context Protocol）本质：开放的通信协议，**用于连接AI与外部系统**（包括数据库、API、文件系统、各种SaaS服务）

  - 架构模式：客户端-服务器架构
    - AI(客户端) ↔ MCP Server（通过 stdio/HTTP 通信）
  - 提供内容：
    - Tools - 可执行的功能（如文件操作、数据库查询）
    - Resources - 外部数据源（如文件系统、API）
    - Prompts - 预定义的提示词模板
  - 特点：
    - 协议标准化，任何语言都可实现服务器
    - 动态通信，**实时交互**
    - 可访问外部系统和实时数据
    - 需要运行独立的服务器进程

  示例：
  - @modelcontextprotocol/server-filesystem - 访问文件系统
  - @modelcontextprotocol/server-github - GitHub API
  - @modelcontextprotocol/server-sqlite - SQLite 数据库

  ## 2.Skills本质：静态知识包，嵌入到 Claude 的上下文中

  - 架构模式：文件系统目录
  skill-name/
  ├── SKILL.md (核心说明)
  ├── scripts/ (可执行脚本)
  ├── references/ (参考文档)
  └── assets/ (输出资源)
  - 提供内容：
    - 领域专家知识
    - 标准化工作流程
    - 可重用的脚本和模板
  - 特点：
    - 纯文本/文件，无需运行服务器
    - 按需加载到上下文
    - 侧重流程指导和领域知识
    - 无外部依赖，易于分发

  示例：
  - commit - 生成符合规范的提交信息
  - frontend-design - 创建前端界面
  - skill-creator - 创建新技能的指南

## 3.Subagent：派出去干活的人。
当你让Claude Code派一个Subagent去做任务时，Claude会新开一个独立的对话会话（有效防止上下文污染）。这个Subagent有自己的上下文窗口、自己的系统提示、自己的工具权限。它干完活，把结果带回来。

实例：
- 审查整个代码仓库（耗时长）
- 同时处理多个独立任务


# Skills分类体系
## 第一层：按来源分Skills来源
├── 官方Skills（Anthropic提供）
│   ├── 文档处理：docx, pdf, pptx, xlsx
│   ├── 医疗健康：FHIR开发, 临床试验协议
│   └── 生命科学：scVI-tools, Nextflow
├── 合作伙伴Skills
│   └── Notion, Atlassian, Figma, Canva, Stripe, Zapier...
└── 自定义Skills
    ├── 社区开源
    └── 个人/团队创建
建议：优先使用官方Skills（安全、维护有保障）合作伙伴Skills按需使用自定义Skills要自己审查安全性

## 第二层：按功能分Skills功能分类
├── 文档与创意
│   ├── 文档生成（PDF/Word/PPT/Excel）
│   ├── 视觉设计（插画、动图）
│   └── 内容创作（品牌指南、风格指南）
├── 开发与工程
│   ├── 前端开发
│   ├── 后端架构
│   ├── 测试质量
│   ├── DevOps
│   └── 代码审查
├── 工作流与自动化
│   ├── 协作流程
│   ├── 知识管理
│   └── 项目管理
└── 垂直领域
    ├── 财务分析
    ├── 法律合规
    ├── 医疗健康
    └── 安全审计
## 第三层：按作用域分Skills作用域
├── 个人级（~/.claude/skills/）
│   └── 个人偏好、通用能力
├── 项目级（.claude/skills/）
│   └── 项目规范、团队约定
└── 组织级（API统一管理）
    └── 企业标准、合规要求


● MCP、Skills、Subagent 对比表
![](Pasted%20image%2020260120132912.png)