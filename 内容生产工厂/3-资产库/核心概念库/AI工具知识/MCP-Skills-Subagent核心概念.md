# MCP、Skills、Subagent 核心概念

## 一、定义

### MCP (Model Context Protocol)
**本质：** 开放的通信协议，用于连接AI与外部系统

**特点：**
- 客户端-服务器架构（AI ↔ MCP Server）
- 提供Tools（可执行功能）、Resources（数据源）、Prompts（提示词模板）
- 动态通信，实时交互
- 需要运行独立的服务器进程

**示例：**
- @modelcontextprotocol/server-filesystem - 访问文件系统
- @modelcontextprotocol/server-github - GitHub API
- @modelcontextprotocol/server-sqlite - SQLite 数据库

---

### Skills
**本质：** 静态知识包，嵌入到Claude的上下文中

**文件结构：**
```
skill-name/
├── SKILL.md (核心指令文件，必需)
├── scripts/ (可执行脚本，可选)
├── references/ (参考文档，可选)
└── assets/ (模板和资源，可选)
```

**渐进式披露机制：**
- AI必读：metadata、instructions
- 按需读取：reference（详细说明）、script（不占上下文）、assets（不占上下文）

**特点：**
- 按需加载到上下文
- 无需外部依赖
- 适合知识封装和工作流定义

---

### Subagent
**本质：** 独立的对话会话，防止上下文污染

**特点：**
- 有自己的上下文窗口
- 有自己的系统提示
- 有自己的工具权限
- 适合耗时任务和多任务并行

---

## 二、使用场景

### MCP适用场景
- ✅ 需要实时访问外部数据（API、数据库、文件系统）
- ✅ 需要执行系统级操作（文件读写、命令执行）
- ✅ 需要与第三方服务集成（GitHub、Slack、Notion）

### Skills适用场景
- ✅ 封装领域知识（学术写作、代码审查、文档生成）
- ✅ 定义标准化工作流（/commit、/review-pr）
- ✅ 创建可复用的模板和规范

### Subagent适用场景
- ✅ 探索型任务（代码库搜索、多文件分析）
- ✅ 耗时任务（测试运行、大规模重构）
- ✅ 并行任务（同时运行多个独立任务）

---

## 三、在历史内容中的应用

### 已发布内容
- 推特帖子：《Agent Skills、MCP、Subagent 深度解析》
- 人生知识库：Agent Skills、MCP、Subagent完整指南

### 引用示例
> "MCP是协议，Skills是知识包，Subagent是独立对话 - 三者各司其职，共同构建Claude Code的能力体系。"

---

## 四、创作时可复用的表达

**对比说明：**
- "MCP像插座，Skills像书本，Subagent像助手"
- "MCP是实时的，Skills是静态的，Subagent是独立的"

**使用场景举例：**
- "如果你需要读取文件，用MCP；如果你需要标准化工作流，用Skills；如果你需要并行处理多个任务，用Subagent。"

**技术栈描述：**
- "Claude Code = MCP（实时数据访问）+ Skills（领域知识封装）+ Subagent（任务并行处理）"

---

## 五、相关资源

**参考文档：**
- E:\obsidian\知识库\AI工具学习\爆火的AI名词概念\Agent Skills、MCP、Subagent.md

**可复用内容：**
- 三者对比图（NotebookLM Mind Map.png）
- 文件结构示例
- 渐进式披露机制说明

---

*最后更新：2026-02*
*使用次数：待统计*
*最佳应用案例：待补充*
